---
title: Az Azure Australia biztonságának ismertetése
description: Az ausztráliai régiókkal kapcsolatos információk, amelyek megfelelnek az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok konkrét követelményeinek.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575460"
---
# <a name="azure-australia-security-explained"></a>Az Azure Australia biztonságának ismertetése

Ez a cikk néhány olyan gyakori kérdést és területet ismertet, amely az ausztráliai kormányzati szervekkel kapcsolatos, az Microsoft Azure Ausztráliában való használatra, tervezésre és üzembe helyezésre szolgáló területekkel foglalkozik.

## <a name="irap-and-certified-cloud-services-list-documents"></a>IRAP és minősített Cloud Services dokumentumok listázása

Az ausztrál Cyber Security Center (ASCS) tanúsítási, minősítési jelentést és használati útmutatót biztosít a szolgáltatáshoz, amikor a tanúsítvány bekerül a Certified Cloud Services listára (CCSL).

A Microsoft jelenleg az Azure, az Office 365 és a Dynamics 365 CRM CCSL van felsorolva.

A Microsoft a [Microsoft szolgáltatás-megbízhatósági portál](https://aka.ms/au-irap)Ausztrália-specifikus oldalán elérhetővé teszi a naplózási, értékelési és ASCs minősítési dokumentumokat az ügyfelek és partnerek számára.

## <a name="dissemination-limiting-markers-and-protected-certification"></a>A közzétételi jelölőket és a védett minősítést korlátozó terjesztési

A kormányzati szervezetek általi használatra jóváhagyott rendszerek, beleértve a Cloud Servicest is, a ASCS által létrehozott és közzétett [információs biztonsági kézikönyvben (ISM)](https://acsc.gov.au/infosec/ism/) határozzák meg. A ASCS a Cyber biztonsági és a Felhőbeli minősítésért felelős ausztrál Signals Igazgatóság (ASD) entitása.

A jóváhagyási folyamat két lépésből áll:

1. **Biztonsági értékelés (IRAP)** : Az a folyamat, amelyben a regisztrált szakemberek a rendszereket, szolgáltatásokat és megoldásokat értékelik az ISM-es műszaki ellenőrzéseken, és kiértékelik, hogy a vezérlők hatékonyan lettek-e implementálva. Az értékelés azt is meghatározza, hogy a jóváhagyó hatóság milyen kockázatokkal jár a jóváhagyás megkezdése előtt.

1. **Jóváhagyás a működéshez**: Az a folyamat, amelyben egy kormányzati szerv vezető tisztviselője hivatalosan felismeri és elfogadja a rendszer fennmaradó kockázatát az informatikai folyamatok, a tárolók és a kommunikáció során. A folyamat bemenete a biztonsági Értékelés.

Az Azure-szolgáltatások védett szinten való értékelése azt jelzi, hogy a védett és az alábbi adatmennyiségek tárolásához és feldolgozásához szükséges biztonsági ellenőrzések megvalósítását megerősítettük, és hatékonyan működnek.

## <a name="australian-data-classification-changes"></a>Ausztrál adatbesorolási változások

2018. október 1-jén a legfőbb ügyészi részleg nyilvánosan bejelentette a védelmi biztonsági házirend keretrendszerének (PSPF) változását, amely konkrétan egy új, [bizalmas és minősített információs rendszer](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Módosított PSPF besorolások](media/pspf-classifications.png)

Ezek a módosítások a PSPF alá tartozó összes ausztráliai Ügynökséget és szervezetet érintik. Az aktuális IRAP/CCSL-minősítéseket érintő elsődleges változás az, hogy az aktuális terjesztési korlátozási jelöléseket (DLMs) kivonták. A hivatalos: A bizalmas adatok védelméhez használt különböző DLMs a szenzitív jelölés váltja fel. A módosítás három olyan adatkezelési jelölőt is bevezetett, amelyek az összes hivatalos információra alkalmazhatók az érzékenység és a besorolás szintjén. A védett besorolás változatlan marad.

A "nem besorolt" kifejezés el lett távolítva az új rendszerből, és a "nem hivatalos" kifejezés a nem kormányzati információkra van alkalmazva, de nem igényel formális jelölést.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Válassza ki a hivatalos Azure-régiót: Bizalmas és védett munkaterhelések

Az Azure hivatalos: A bizalmas és védett minősítésű szolgáltatások mind a négy ausztráliai adatközpont-régióban üzembe helyezhetők: Kelet-Ausztrália, Kelet-Ausztrália, Közép-Ausztrália és Ausztrália középső régiója 2. A ASCS által kiadott minősítési jelentés azt javasolja, hogy a védett adatközpontok a Canberra Azure Government régióiban legyenek telepítve, ha egy szolgáltatás elérhető. A védett tanúsítvánnyal rendelkező Azure-szolgáltatásokkal kapcsolatos további információkért tekintse [meg az Australia oldalt a szolgáltatás](https://aka.ms/au-irap)-megbízhatósági portálon.

>[!NOTE]
>A Microsoft azt javasolja, hogy az összes érzékenységet és besorolást központilag központilag telepítse az Ausztrália középső és ausztráliai középső régiójában, mivel azokat kifejezetten a kormányzat igényeihez tervezték és üzemeltetik.

További információ az Azure ausztráliai régióinak speciális jellegéről: az [Azure Australia középső régiói](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Hogyan különíti el a Microsoft a besorolt és a hivatalos adatgyűjtést

A Microsoft az Azure-t és az Office 365-et Ausztráliában működteti, mintha az összes adat bizalmas vagy besorolt legyen, ami a biztonsági ellenőrzéseket az adott magas sávon is felveti.

Az Azure-t támogató infrastruktúra több besorolást is kiszolgálhat. Mivel feltételezzük, hogy az ügyféladatokat besorolták, a megfelelő vezérlők vannak érvényben. A Microsoft olyan alapszintű biztonsági testhelyzetet vezetett be a szolgáltatásainkhoz, amely megfelel a védett minősített információk tárolására és feldolgozására vonatkozó PSPF követelményeknek.

Annak biztosítása érdekében, hogy az Azure egyik bérlője ne legyen kockázat más bérlők számára, a Microsoft átfogó védelmi jellegű ellenőrzéseket valósít meg.

A Microsoft Azure platformon belül megvalósított képességek mellett további, az ügyfél által felügyelt kulcsokkal, a beágyazott virtualizálás és az igény szerinti rendszergazdai hozzáférésekkel való titkosítás is csökkentheti a kockázatokat. A Canberra-beli Azure Government ausztráliai régiókban a formális engedélyezési folyamat csak az ausztrál és az új-zélandi kormányt, valamint az országos kritikus fontosságú infrastruktúrát biztosító szervezeteket helyezi üzembe. Ez a közösségi felhő további garanciát nyújt a többek között a bérlői kockázatokra érzékeny szervezeteknek.

A Microsoft Azure védett minősítési jelentés megerősíti, hogy ezek a vezérlők érvényesek a védett minősített adatmennyiségek tárolásához és feldolgozásához, valamint azok elkülönítéséhez.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Az IRAP/CCSL relevanciája az állami kormányzati és a kritikus fontosságú infrastruktúra-szolgáltatók számára

Számos állami kormányzati és kritikus fontosságú infrastruktúra-szolgáltató a szövetségi kormányzati követelményeket beépíti a biztonsági szabályzatba és a megbízhatósági keretrendszerbe. Ezek a szervezetek emellett a hivatalos, a hivatalos: Bizalmas, és bizonyos mennyiségű védett besorolású adat a szövetségi kormánnyal vagy a saját jogon való interakcióval.

Az ausztrál kormány egyre inkább a szabályzatokat és a jogszabályokat a nem kormányzati adatvédelemre összpontosítja, amely alapvetően hatással van Ausztráliában a biztonságra és a gazdasági fellendülésre. Ennek megfelelően az Azure Australia-régiók és a CCSL-minősítés az összes említett iparágra vonatkozik.

![Kritikus infrastruktúra-ágazatok](media/nci-sectors.png)

A Microsoft minősítései azt mutatják be, hogy az Azure-szolgáltatások alapos, szigorú és formális vizsgálatot végeztek a biztonsági védelem terén, és ezeket a szigorúan bizalmas adatok kezelésére hagyták jóvá.

## <a name="location-and-control-of-microsoft-data-centres"></a>A Microsoft adatközpontjainak helye és felügyelete

A kormányzati és a kritikus fontosságú infrastruktúra kötelező követelménye, hogy explicit módon tájékoztassa az adatközpont helyét és tulajdonjogát a Cloud Services számára az adatfeldolgozás során. A Microsoft egyedülálló nagy kapacitású-szolgáltatóként, amely részletes információkat nyújt ezekről a helyekről és tulajdonosokról.

A Microsoft Azure Australia-régiói (Ausztrália középső és Ausztrália középső régiója 2) a CDC Datacentres eszközein belül működnek. A CDC Datacentres tulajdonosa a nemzetközösségi járadék Corporation 48%-os tulajdonjoga, 48%-os tulajdonjoga a Infratil-től (Új-Zéland-alapú, kettős ausztráliai és új-zélandi Értéktőzsde a hosszú távú infrastruktúrában Asset Fund) és 4% ausztrál felügyelet. 

A CDC Datacentres felügyeletét olyan szerződéses garanciákkal látja el, amelyek a tulajdonjog és a felügyelet jövőbeli átruházását korlátozzák. Az ellátási lánc és a Microsoft a CDC Datacentres való együttműködésének átláthatósága összhangban van a [teljes kormányzati üzemeltetési stratégia](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) és a Certified szuverén adatközpontot definíciójának alapelveivel.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>Az aktuális CCSL-minősítés részét képező Azure-szolgáltatások

2017 júniusában a ASCS Certified 41 Azure-szolgáltatások az adattároláshoz és-feldolgozáshoz a nem besorolt módon: DLM szint. A fenti szolgáltatások 24. április 2018-án a védett besorolású adatmennyiséget tanúsították.

Az ausztráliai Azure-régiókban elérhető ASCS-tanúsítvánnyal rendelkező Azure-szolgáltatások rendelkezésre állása a következő (a félkövéren látható szolgáltatások a védett szinten vannak hitelesítve).

|Azure Australia – középső régiók|Nem regionális szolgáltatások és más régiók|
|---|---|
|API Management, app Gateway, alkalmazásszolgáltatás, **Automation**, **Azure Portal**, **Backup**, **Batch**, **Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, napló Elemzés, **többtényezős hitelesítés**, Redis cache, **erőforrás-kezelő**, **Service Bus**, **Service Fabric**, **site Recovery**, **SQL Database**, **tárolás**, Traffic Manager, **virtuális Gépek**, **Virtual Network**, **VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **Import export**, **Information Protection**, **IOT hub**, Machine learning, Media Services, **Notification Hubs**, Power bi, **ütemező**, **Security Center**, Keresés, Stream Analytics|
|

A Microsoft közzéteszi [Microsoft Azure megfelelőségének áttekintését](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) , amely felsorolja az Azure-ba irányuló összes globális, kormányzati, iparági és regionális megfelelőségi és értékelési folyamatot, amely magában foglalja a IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>Az Azure-szolgáltatás nem szerepel a szükségesnél alacsonyabb szinten, vagy nem értékelték

A minősítéssel nem rendelkező vagy a hivatalos címen hitelesített szolgáltatások: Bizalmas, de nem a védett szint, a védett adatokat üzemeltető megoldás részeként vagy a következők egyike is használható:

- Nem titkosított védett adattárolók tárolása vagy feldolgozása, vagy
- Elvégezte a kockázatértékelést, és jóváhagyta a szolgáltatást, hogy saját maga tárolja a védett adatot.

Használhat olyan szolgáltatást, amely nem szerepel a CCSL a hivatalos adattárolók tárolására és feldolgozására, de az ISM-nek szüksége van arra, hogy írásban értesítse a ASCS, mielőtt megkezdi a szerződést egy felhőalapú szolgáltatóval való belépés vagy megújítása előtt.

Az Ügynökség által védett munkaterhelésekhez használt szolgáltatásoknak biztonsági értékelést és jóváhagyást kell biztosítaniuk, összhangban az ISM-ben és az Ügynökség által felügyelt IRAP felmérések folyamatában ismertetett folyamatokkal az [DTA biztonságos felhőalapú stratégiájában](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![DTA biztonságos felhőalapú stratégia minősítési folyamata](media/certification.png)

A Microsoft folyamatosan felméri a szolgáltatásainkat, hogy biztosítsák a platform biztonságos és célnak megfelelő használatát az ausztrál kormány általi használatra. Vegye fel a kapcsolatot a Microsofttal, ha olyan szolgáltatással kapcsolatos segítségre van szüksége, amely jelenleg nem a CCSL védett szinten található.

Mivel a Microsoft számos olyan szolgáltatást biztosít a CCSL, amely a nem besorolt DLM és a védett besorolások esetében egyaránt engedélyezett, az ISM megköveteli, hogy legalább kétévente IRAP értékelést készítsen szolgáltatásainkról. A Microsoft éves értékelést készít, amely szintén lehetőséget nyújt további szolgáltatások megfontolására.

## <a name="certified-protected-gateway-in-azure"></a>Hitelesített védett átjáró az Azure-ban

A Microsoft nem működtet kormányzati tanúsítvánnyal rendelkező biztonságos internet-átjárót (SIG) az átjáró-konszolidációs program keretében megengedett SIGs számának korlátozásai miatt. A SIG várt és szükséges képességei azonban Microsoft Azureon belül konfigurálhatók.

Az Azure-szolgáltatások védett tanúsítványán keresztül a ASCS konkrét ajánlásokat tartalmaz az ügynökségeknek az Azure-hoz való csatlakozáshoz és a biztonsági tartományok közötti hálózati szegmentálás megvalósításához, például a védett és az internet között. Ezek a javaslatok a hálózati biztonsági csoportok, tűzfalak és virtuális magánhálózatok használatát foglalják magukban. A ASCS egy virtuális átjáró berendezés használatát javasolja. Az Azure-ban több virtuális készülék is rendelkezésre áll, amelyek fizikai egyenértékűek az ASD kiértékelt termékeinek listájával, vagy kiértékelésre kerültek a közös feltételekkel foglalkozó portálon. Ezeket a termékeket az ASD kölcsönösen elismeri az általános feltételek felismerési megállapodásának aláírói számára.

A Microsoft útmutatást hozott létre az Azure-alapú képességek megvalósításához, amelyek biztosítják a különböző biztonsági tartományok közötti határ védelméhez szükséges biztonsági funkciókat, amelyek együttes használata esetén a hitelesített SIG egyenértékűnek kell lennie. Számos partner tud segítséget nyújtani ezen képességek megtervezésében és megvalósításában, és számos olyan partneri megoldás érhető el, amely ugyanezt teszi lehetővé.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>A Microsoft támogatási szakemberének biztonsági felhatalmazása és állampolgársága

A Microsoft a szolgáltatásokat globálisan, a betanított és képzett biztonsági személyzettel működteti. A Sydney-ben és Melbourne-ben található létesítményekhez nem megfelelő fizikai hozzáféréssel rendelkező munkatársak az ausztrál kormány alapkonfigurációjának biztonsági engedélyekkel rendelkeznek. Az Ausztrália középső és ausztráliai középső régiójának tagjai legalább negatív, 1 (NV1) (a titkos adatvédelemhez megfelelő) (az adott esetben a titkos) területekre érvényesek. Ezek a kilépési követelmények további garanciát biztosítanak azon ügyfeleknek, akik az Azure-t üzemeltető adatközpontokban lévő munkatársak igen megbízhatóak.

A Microsoft nem rendelkezik olyan állandó hozzáférési házirenddel, amelynek hozzáférését a rendszer egy időben, a szerepköralapú hozzáférés-vezérlésen alapulva elég adminisztrációt biztosít. Az esetek túlnyomó többségében a rendszergazdáknak nincs szükségük az ügyféladatok elérésére, illetve a szolgáltatással kapcsolatos hibák megoldására. A távoli végrehajtáshoz szükséges feladatok nagy fokú automatizálása és parancsfájlkezelése cáfolja az ügyféladatok közvetlen elérésének szükségességét.

A legfőbb ügyészi részleg megerősítette, hogy az Azure-ban a Microsoft személyzeti biztonsági szabályzatai és eljárásai összhangban vannak azzal, hogy a PSPF az INFOSEC-9-ben az információkhoz való hozzáféréshez való hozzáférést biztosít.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>A fegyverek nemzetközi forgalmának (ITAR) és az exportálási szabályzatok (EAR) adatok tárolása

Az Azure-beli technikai vezérlők, amelyek segítik az ügyfeleknek az export-vezérelt adatkezelésre vonatkozó kötelezettségeiknek való megfelelést, globálisak az Azure-ban. Fontos, hogy a rendszer nem rendelkezik formális értékelési és minősítési keretrendszerrel az export-vezérelt adatkezeléshez.

A Azure Government és az Office 365 Amerikai Egyesült államokbeli kormányzati Minisztérium további szerződéses és feldolgozási intézkedéseket helyezett el, hogy támogassa az export Controls által érintett ügyfeleket. Ezek a további szerződéses záradékok és az Azure-régiók garantált állami támogatása és felügyelete nem áll rendelkezésre Ausztráliában.

Ez nem jelenti azt, hogy Ausztráliában nem használható az Azure az ITAR/EAR számára, de világosan meg kell ismernie az exportálási licenccel kapcsolatos korlátozásokat. Az Azure-ban az adattároláshoz további védelmi eszközöket is meg kell valósítania az ilyen kötelezettségek teljesítéséhez. Előfordulhat például, hogy a következőket kell tennie:

- Hozzon létre egy állampolgárságot attribútumként a Azure Active Directory.
- A Azure Information Protection használatával kikényszerítheti az adattitkosítási szabályokat, és korlátozhatja azt csak az Egyesült Államokban, illetve más nemzetiségűek is az exportálási licencben.
- Az összes helyszíni adattitkosítás az Azure-ban való tárolás előtt, az ügyfél kulcsa alapján, vagy a saját kulcs ITAR-adataihoz való tárolása előtt.

Mivel a ITAR nem formális minősítés, meg kell ismernie, hogy az exportálási licenccel kapcsolatos korlátozások és korlátozások milyenek. Ezt követően elvégezheti a munkát, hogy az Azure-ban elegendő-e a követelmények teljesítése. Ebben az esetben az egyik fontos kérdés, hogy a mérnökök hozzáférhetnek az exportálási licenccel nem rendelkező állampolgársághoz.

## <a name="next-steps"></a>További lépések

 Az Azure-beli VPN-kapcsolatok ISM-kompatibilis konfigurálásához és megvalósításához lásd: [azure VPN Gateway](vpn-gateway.md).
