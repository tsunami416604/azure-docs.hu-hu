---
title: Folyamatos üzletmenet és vészhelyreállítás
description: Tervezze meg stratégiáját az adatok védelme érdekében, gyorsan helyreállítva a zavaró eseményektől, a kritikus üzleti funkciók által igényelt erőforrások helyreállításához és az Azure Logic Apps üzletmenetfolytonosságának fenntartásához
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437705"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Üzletmenet-folytonosság és vészhelyreállítás az Azure Logic Apps alkalmazásokhoz

Az előre nem látható események nek az üzletre és az ügyfelekre gyakorolt hatásának és hatásainak csökkentése érdekében győződjön meg arról, hogy [ *vész-helyreállítási* (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) megoldás van érvényben az adatok védelme érdekében, gyorsan visszaállíthatja a kritikus üzleti funkciókat támogató erőforrásokat, és a műveleteket az [ *üzletmenet folytonosságának* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)fenntartása érdekében. A fennakadások közé tartozhatpéldául az alapinfrastruktúra vagy az összetevők , például a tárolás, a hálózat vagy a számítási erőforrások, a helyreállíthatatlan alkalmazáshibák vagy akár az adatközpont teljes elvesztése. Az üzletmenet-folytonossági és vész-helyreállítási (BCDR) megoldás sal való készen való felkészüléssel a vállalat vagy a szervezet gyorsabban reagálhat a megszakításokra, a tervezett vagy nem tervezett re, és csökkentheti az ügyfelek állásidejét.

Ez a cikk bcdr útmutatást és stratégiákat tartalmaz, amelyeket az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)használatával automatizált munkafolyamatok létrehozásakor alkalmazhat. A logikai alkalmazások munkafolyamatai segítségével könnyebben integrálhatja és vezényelheti az adatokat az alkalmazások, a felhőszolgáltatások és a helyszíni rendszerek között azáltal, hogy csökkenti az íráshoz szükséges kód mennyiségének csökkentését. Ha a BCDR-t tervezi, ügyeljen arra, hogy ne csak a logikai alkalmazásokat vegye figyelembe, hanem a logikai alkalmazásokkal használt Azure-erőforrásokat is:

* A logikai alkalmazásokból más alkalmazásokba, szolgáltatásokba és rendszerekbe létrehozott [kapcsolatok.](../connectors/apis-list.md) További információt a témakör későbbi témakörében, az [Erőforrásokkal való kapcsolatok](#resource-connections) című témakörben talál.

* [Helyszíni adatátjárók,](../logic-apps/logic-apps-gateway-connection.md) amelyek Azure-erőforrások, amelyeket a logikai alkalmazásokban hoz létre és használ a helyszíni rendszerekben lévő adatok eléréséhez. Minden átjáróerőforrás egy külön [adatátjáró-telepítést](../logic-apps/logic-apps-gateway-install.md) jelent a helyi számítógépen. További információ: A témakör [későbbi, helyszíni adatátjárói.](#on-premises-data-gateways)

* [Integrációs fiókok,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ahol meghatározza és tárolja az összetevők, amelyek et a logikai alkalmazások a [vállalati integrációs](../logic-apps/logic-apps-enterprise-integration-overview.md) forgatókönyvek logikai alkalmazások használata. Beállíthat például [régiók közötti vészhelyreállítást az integrációs fiókokhoz.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Integrációs szolgáltatáskörnyezetek (ISEs),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ahol hozzon létre logikai alkalmazásokat, amelyek egy elszigetelt Logic Apps futásidejű példányban fut nak egy Azure virtuális hálózaton belül. Ezek a logikai alkalmazások ezután hozzáférhetnek az adott virtuális hálózat tűzfala mögött védett erőforrásokhoz.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Elsődleges és másodlagos helyek

Minden logikai alkalmazásnak meg kell adnia a központi telepítéshez használni kívánt helyet. Ez a hely vagy egy nyilvános régió a globális több-bérlős Azure-ban, például a "West USA", vagy egy integrációs szolgáltatási környezet (ISE), amely korábban létrehozott és üzembe helyezett egy Azure virtuális hálózatba. Logikai alkalmazások futtatása egy ISE hasonló a globális Azure-régióban futó logikai alkalmazások, ami azt jelenti, hogy a vész-helyreállítási stratégia mindkét forgatókönyvre alkalmazható. Az internet-hozzáférést igénybe vevő szervezeteknek azonban más szempontok is vannak, például a csak az ISE-k számára elérhető erőforrásokhoz való hozzáférés konfigurálása.

> [!NOTE]
> Ha a logikai alkalmazás b2B-összetevőkkel is működik, például a kereskedelmi partnerekkel, a megállapodásokat, a sémákat, a leképezéseket és a tanúsítványokat, amelyek egy integrációs fiókban tárolódnak, mind az integrációs fióknak, mind a logikai alkalmazásoknak meg kell adniuk ugyanazt a helyet.

Ez a vész-helyreállítási stratégia az elsődleges logikai alkalmazás [*feladatátvételi*](https://en.wikipedia.org/wiki/Failover) beállítása egy készenléti vagy biztonsági mentési logikai alkalmazásra egy másik helyen, ahol az Azure Logic Apps is elérhető. Így, ha az elsődleges veszteségeket, zavarokat vagy hibákat szenved, a másodlagos átveheti a munkát. Ez a stratégia megköveteli, hogy a másodlagos logikai alkalmazás és a függő erőforrások már üzembe vannak helyezve, és készen áll a másik helyen.

Ha a helyes DevOps-eljárásokat követi, már használja [az Azure Resource Manager-sablonokat](../azure-resource-manager/management/overview.md) a logikai alkalmazások és a függő erőforrások definiálására és üzembe helyezésére. Az Erőforrás-kezelő sablonjai lehetővé teszik egyetlen központi telepítés definíciójának használatát, majd paraméterfájlok használatát az egyes központi telepítési célokhoz használandó konfigurációs értékek megadásához. Ez a képesség azt jelenti, hogy ugyanazt a logikai alkalmazást különböző környezetekben, például fejlesztésre, tesztelésre és éles környezetre is telepítheti. Ugyanazt a logikai alkalmazást különböző Azure-régiókban vagy IS-e-kben is telepítheti, amelyek támogatják a [párosított régiókat](../best-practices-availability-paired-regions.md)használó vész-helyreállítási stratégiákat.

A feladatátvételi stratégia, a logikai alkalmazások és helyek meg kell felelnie ezeknek a követelményeknek:

* A másodlagos logikai alkalmazáspéldány ugyanazokhoz az alkalmazásokhoz, szolgáltatásokhoz és rendszerekhez fér hozzá, mint az elsődleges logikai alkalmazáspéldány.

* Mindkét logikai alkalmazáspéldány azonos gazdagéptípussal rendelkezik. Így vagy mindkét példány a globális több-bérlős Azure régióiban van telepítve, vagy mindkét példány telepítve van az IS-e-kszámára, amelyek lehetővé teszik a logikai alkalmazások számára, hogy közvetlenül hozzáférjenek az Azure virtuális hálózat erőforrásaihoz. Az ajánlott eljárásokat és a BCDR párosított régióival kapcsolatos további információkat az [Üzletmenet folytonossága és a vész-helyreállítás (BCDR): Azure párosított régiók című témakörben](../best-practices-availability-paired-regions.md)talál.

  Például mind az elsődleges, mind a másodlagos helyek nek isi-be kell jenek, amikor az elsődleges logikai alkalmazás egy ISE-ben fut, és [ISE-verziójú összekötőket](../connectors/apis-list.md#ise-connectors)használ, HTTP-műveleteket az Azure virtuális hálózat erőforrásainak hívásához, vagy mindkettő. Ebben a forgatókönyvben a másodlagos logikai alkalmazás is rendelkeznie kell egy hasonló beállítás a másodlagos helyen, mint az elsődleges logikai alkalmazás.

  > [!NOTE]
  > A speciálisabb forgatókönyvek, több-bérlős Azure és egy ISE helyként is keverheti. Győződjön meg azonban arról, hogy figyelembe veszi és megértette a [logikai alkalmazások ISE-ben és a több-bérlős Azure-ban való futtatásának közötti különbségeket.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Ha ises-eket használ, [győződjön meg arról, hogy azok horizontálisan ki vannak méretezve, vagy elegendő kapacitással rendelkeznek](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) a terhelés kezeléséhez.

#### <a name="example-multi-tenant-azure"></a>Példa: Több-bérlős Azure

Ebben a példában az elsődleges és másodlagos logikai alkalmazáspéldányok, amelyek a globális több-bérlős Azure-ban ebben a forgatókönyvben üzembe helyezett külön régiókban. Egyetlen [Erőforrás-kezelő sablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) határozza meg a logikai alkalmazáspéldányok és a függő erőforrások at is, amelyeket ezek a logikai alkalmazások igényelnek. A különálló paraméterfájlok határozzák meg az egyes telepítési helyeken használandó konfigurációs értékeket:

![Elsődleges és másodlagos logikai alkalmazáspéldányok különböző helyeken](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Példa: Integrációs szolgáltatás környezete

Ebben a példában az előző elsődleges és másodlagos logikai alkalmazáspéldányokat mutatja be, de külön ISE-kbe van telepítve. Egyetlen Erőforrás-kezelő sablon határozza meg mind a logikai alkalmazáspéldányok, a függő erőforrások által igényelt logikai alkalmazások, és az ise-k, mint a központi telepítési helyek. A különálló paraméterfájlok határozzák meg az egyes helyeken a központi telepítéshez használandó konfigurációs értékeket:

![Elsődleges és másodlagos logikai alkalmazások különböző helyeken](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Az erőforrásokkal való kapcsolatok

Az Azure Logic Apps [beépített eseményindítókat és műveleteket, valamint több száz felügyelt összekötőt](../connectors/apis-list.md) biztosít, amelyeket a logikai alkalmazás más alkalmazásokkal, szolgáltatásokkal, rendszerekkel és más erőforrásokkal, például Azure Storage-fiókokkal, SQL Server-adatbázisokkal, Office 365 Outlook-e-mail fiókokkal és így tovább használhat. Ha a logikai alkalmazásnak hozzáférésre van szüksége ezekhez az erőforrásokhoz, hozzon létre kapcsolatokat, amelyek hitelesítik az erőforrásokhoz való hozzáférést. Minden kapcsolat egy külön Azure-erőforrás, amely egy adott helyen létezik, és más helyeken lévő erőforrások nem használhatók.

A vész-helyreállítási stratégia, fontolja meg a helyeket, ahol függő erőforrások léteznek a logikai alkalmazás példányai:

* Az elsődleges példány és a függő erőforrások különböző helyeken léteznek. Ebben az esetben a másodlagos példány csatlakozhat ugyanazokhoz a függő erőforrásokhoz vagy végpontokhoz. Azonban létre kell hoznia kapcsolatokat kifejezetten a másodlagos példányhoz. Így, ha az elsődleges hely elérhetetlenné válik, a másodlagos kapcsolatok nem érinti.

  Tegyük fel például, hogy az elsődleges logikai alkalmazás egy külső szolgáltatáshoz, például a Salesforce-hoz csatlakozik. Általában a külső szolgáltatás rendelkezésre állása és helye független a logikai alkalmazás rendelkezésre állásától. Ebben az esetben a másodlagos példány csatlakozhat ugyanahhoz a szolgáltatáshoz, de rendelkeznie kell a saját kapcsolat.

* Az elsődleges példány és a függő erőforrások is ugyanazon a helyen léteznek. Ebben az esetben a függő erőforrások nak rendelkezniük kell biztonsági másolatokkal vagy replikált verziókkal egy másik helyen, hogy a másodlagos példány továbbra is hozzáférhessen ezekhez az erőforrásokhoz.

  Tegyük fel például, hogy az elsődleges logikai alkalmazás csatlakozik egy szolgáltatáshoz, amely ugyanabban a helyen vagy régióban található, például az Azure SQL Database. Ha ez a teljes régió elérhetetlenné válik, az Azure SQL Database szolgáltatás az adott régióban is valószínűleg nem érhető el. Ebben az esetben azt szeretné, hogy a másodlagos példány egy replikált vagy biztonsági másolat adatbázist, valamint egy külön kapcsolatot az adatbázishoz.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Helyszíni adatátjárók

Ha a logikai alkalmazás több-bérlős Azure-ban fut, és hozzáférést igényel a helyszíni erőforrásokhoz, például az SQL Server-adatbázisokhoz, telepítenie kell a [helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) egy helyi számítógépre. Ezután létrehozhat egy adatátjáró-erőforrást az Azure Portalon, hogy a logikai alkalmazás használhassa az átjárót, amikor kapcsolatot hoz létre az erőforrással.

Az adatátjáró-erőforrás egy helyhez vagy Az Azure-régióhoz van társítva, csakúgy, mint a logikai alkalmazás-erőforrás. A vész-helyreállítási stratégia győződjön meg arról, hogy az adatátjáró továbbra is elérhető a logikai alkalmazás számára használható. Engedélyezheti [az átjáró magas rendelkezésre állását,](../logic-apps/logic-apps-gateway-install.md#high-availability) ha több átjáró-telepítéssel rendelkezik.

> [!NOTE]
> Ha a logikai alkalmazás integrációs szolgáltatási környezetben (ISE) fut, és csak ISE-verziójú összekötőket használ a helyszíni adatforrásokhoz, nincs szüksége az adatátjáróra, mert az ISE-összekötők közvetlen hozzáférést biztosítanak a helyszíni erőforráshoz.
>
> Ha a kívánt helyszíni erőforráshoz nem érhető el ISE-verziójú összekötő, a logikai alkalmazás továbbra is létrehozhatja a kapcsolatot egy nem ISE-összekötő használatával, amely a globális több-bérlős Azure-ban fut, nem az ISE-ben. Ez a kapcsolat azonban a helyszíni adatátjárót igényli.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Aktív-aktív és aktív-passzív szerepkörök

Beállíthatja az elsődleges és másodlagos helyeket, hogy a logikai alkalmazáspéldányok ezeken a helyeken a következő szerepköröket játszhassák:

| Elsődleges-másodlagos szerepkör | Leírás |
|------------------------|-------------|
| *Aktív-aktív* | Az elsődleges és másodlagos logikai alkalmazáspéldányok mindkét helyen aktívan kezelik a kérelmeket a következő minták valamelyikével: <p><p>- *Terheléselosztás:* Mindkét példány figyelheti a végpontot, és szükség szerint minden példányra vonatkozó terheléselosztási forgalmat. <p>- *Versengő fogyasztók:* Mindkét példány versengő fogyasztóként működhet, így a példányok versenghetnek a várólistából érkező üzenetekért. Ha egy példány meghibásodik, a másik példány veszi át a számítási feladatot. |
| *Aktív-passzív* | Az elsődleges logikai alkalmazáspéldány aktívan kezeli a teljes számítási feladatot, míg a másodlagos példány passzív (letiltva vagy inaktív). A másodlagos vár egy jel, hogy az elsődleges nem érhető el, vagy nem működik a megszakítás vagy hiba miatt, és átveszi a számítási feladatok, mint az aktív példány. |
| Kombináció | Egyes logikai alkalmazások aktív-aktív szerepkört játszanak, míg más logikai alkalmazások aktív-passzív szerepkört játszanak. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Aktív-aktív példák

Ezek a példák az aktív-aktív telepítést mutatják be, ahol mindkét logikai alkalmazáspéldány aktívan kezeli a kérelmeket vagy üzeneteket. Más rendszer vagy szolgáltatás elosztja a kérelmeket vagy üzeneteket a példányok között, például az alábbi lehetőségek egyike:

* Egy "fizikai" terheléselosztó, például egy hardver, amely a forgalmat

* Egy "puha" terheléselosztó, például [az Azure Load Balancer](../load-balancer/load-balancer-overview.md) vagy az [Azure API Management.](../api-management/api-management-key-concepts.md) Az API Management segítségével olyan házirendeket adhat meg, amelyek meghatározzák a bejövő forgalom terhelésének terhelését. Vagy használhat olyan szolgáltatást is, amely támogatja az állapotkövetést, például az [Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

  Bár ez a példa elsősorban az Azure Load Balancer-t mutatja, használhatja a forgatókönyv igényeinek leginkább megfelelő lehetőséget:

  !["Aktív-aktív" beállítás, amely terheléselosztót vagy állapotalapú szolgáltatást használ](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Minden logikai alkalmazáspéldány fogyasztóként működik, és mindkét példány verseng a várólistából érkező üzenetekért:

  !["Aktív-aktív" beállítás, amely "versengő fogyasztókat" használ](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktív-passzív példák

Ebben a példában az aktív-passzív beállítás, ahol az elsődleges logikai alkalmazáspéldány aktív egy helyen, míg a másodlagos példány inaktív marad egy másik helyen. Ha az elsődleges megszakítást vagy hibát tapasztal, egy operátor futtathat egy parancsfájlt, amely aktiválja a másodlagos a számítási feladatok at.

!["Aktív-passzív" beállítás, amely "versengő fogyasztókat" használ](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Kombinálva aktív-aktív és aktív-passzív

Ebben a példában egy kombinált beállítás, ahol az elsődleges hely rendelkezik mindkét aktív logikai alkalmazáspéldányok, míg a másodlagos hely aktív-passzív logikai alkalmazáspéldányok. Ha az elsődleges hely megszakítást vagy hibát tapasztal, a másodlagos helyen lévő aktív logikai alkalmazás, amely már részleges számítási feladatokat kezel, átveheti a teljes számítási feladatot.

* Az elsődleges helyen egy aktív logikai alkalmazás figyeli az Azure Service Bus várólistáját az üzenetekhez, míg egy másik aktív logikai alkalmazás az Office 365 Outlook lekérdezési eseményindítójával ellenőrzi az e-maileket.

* A másodlagos helyen egy aktív logikai alkalmazás együttműködik a logikai alkalmazás az elsődleges helyen figyelve és versengő üzenetek et ugyanabból a Service Bus-várólistából. Eközben egy passzív inaktív logikai alkalmazás készenléti állapotban várja az e-mailek ellenőrzését, amikor az elsődleges hely elérhetetlenné válik, de le van *tiltva* az e-mailek újraolvasásának elkerülése érdekében.

!["Aktív-passzív" és "aktív-passzív" kombináció, amely ismétlődési eseményindítókat használ](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Logikai alkalmazás állapota és előzményei

Amikor a logikai alkalmazás aktiválódik, és elindul, az alkalmazás állapota ugyanazon a helyen tárolódik, ahol az alkalmazás elindult, és nem átruházható egy másik helyre. Ha hiba vagy megszakítás történik, a folyamatban lévő munkafolyamat-példányok megszűnnek. Ha beállított egy elsődleges és másodlagos helyet, az új munkafolyamat-példányok a másodlagos helyen kezdenek futni.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Elhagyott folyamatban lévő példányok csökkentése

Az elhagyott folyamatban lévő munkafolyamat-példányok számának minimalizálása érdekében különböző üzenetminták közül választhat, például:

* [Rögzített útválasztási bizonylatmintázata](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Ez a vállalati üzenetminta, amely egy üzleti folyamatot kisebb szakaszokra oszt fel. Minden fázisban egy logikai alkalmazást állít be, amely kezeli az adott szakasz munkaterhelését. Az egymással való kommunikációhoz a logikai alkalmazások egy aszinkron üzenetkezelési protokollt, például az Azure Service Bus-várólistákat vagy témaköröket használnak. Ha egy folyamatot kisebb szakaszokra oszt, csökkenti azoknak az üzleti folyamatoknak a számát, amelyek elakadhatnak egy sikertelen logikai alkalmazáspéldányon. Erről a mintáról további általános információt a [Vállalati integrációs minták – Útválasztási levél .](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)

  Ez a példa egy útválasztási slip mintát mutat be, amelyben minden logikai alkalmazás egy szakaszt jelöl, és egy Service Bus-várólistát használ a folyamat következő logikai alkalmazásával való kommunikációhoz.

  ![Az üzleti folyamat felosztása logikai alkalmazások által képviselt szakaszokra, amelyek az Azure Service Bus-várólisták használatával kommunikálnak egymással](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Ha mind az elsődleges, mind a másodlagos logikai alkalmazáspéldányok ugyanazt az útválasztási slip mintát követik a helyükön, a [versengő fogyasztói minta](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) megvalósíthatja az [aktív-aktív szerepkörök](#roles) beállításával ezeket a példányokat.

* [Folyamatkezelő (bróker) minta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Betekintés-zárolás időbeli meghosszabbítási minta nélkül](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Hozzáférés az előzmények aktiválásához és futtatásához

Ha további információt szeretne kapni a logikai alkalmazás korábbi munkafolyamat-végrehajtásairól, tekintse át az alkalmazás eseményindítóját, és futassa az előzményeket. A logikai alkalmazás előzményvégrehajtási előzményei ugyanabban a helyen vagy régióban vannak tárolva, ahol a logikai alkalmazás futott, ami azt jelenti, hogy nem telepítheti át ezt az előzményeket egy másik helyre. Ha az elsődleges példány átadja a feladatát egy másodlagos példány, csak az egyes példányok eseményindítójának eléréséhez férhet hozzá, és az előzményeket futtatja a megfelelő helyeken, ahol ezek a példányok futottak. Azonban a logikai alkalmazás előzményeiről helyfüggetlen információkat kaphat, ha beállítja a logikai alkalmazásokat diagnosztikai események küldésére egy Azure Log Analytics-munkaterületre. Ezután tekintse át az állapotát és az előzményeket a több helyen futó logikai alkalmazások között.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Eseményindító típusának útmutatása

A logikai alkalmazásokban használt eseményindító-típus határozza meg a logikai alkalmazások beállításának lehetőségeit a vész-helyreállítási stratégia különböző helyeken. A logikai alkalmazásokban az elérhető eseményindító-típusok a következők:

* [Ismétlődés iszaktiválása](#recurrence-trigger)
* [Lekérdezési eseményindító](#polling-trigger)
* [Kérelem eseményindító](#request-trigger)
* [Webhook-eseményindító](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Ismétlődés iszaktiválása

Az **Ismétlődés** eseményindító független bármely adott szolgáltatástól vagy végponttól, és kizárólag egy megadott ütemezés alapján aktiválódik, és nincs más feltétel, például:

* Rögzített gyakoriság és időköz, például 10 percenként
* Fejlettebb ütemterv, mint például minden hónap utolsó hétfője 17:00-kor

Amikor a logikai alkalmazás egy ismétlődési eseményindítóval kezdődik, be kell állítania az elsődleges és másodlagos logikai alkalmazáspéldányokat az [aktív-passzív szerepkörökkel.](#roles) A *helyreállítási idő célkitűzésének* (RTO) csökkentése érdekében, amely egy üzleti folyamat megszakítás vagy katasztrófa utáni visszaállításának célidőtartamára utal, beállíthatja a logikai alkalmazáspéldányokat [az aktív-passzív és](#roles) a [passzív-aktív szerepkörök](#roles)kombinációjával. Ebben a beállításban az ütemezést különböző helyek között osztja fel.

Tegyük fel például, hogy rendelkezik egy logikai alkalmazás, amely 10 percenként kell futtatni. A logikai alkalmazásokat és helyeket beállíthatja úgy, hogy ha az elsődleges hely elérhetetlenné válik, a másodlagos hely átveheti a munkát:

!["Aktív-passzív" és "passzív-aktív" kombináció, amely ismétlődési eseményindítókat használ](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Az elsődleges helyen állítson be [aktív-passzív szerepköröket](#roles) ezekhez a logikai alkalmazásokhoz:

  * Az *aktív* engedélyezett logikai alkalmazás esetén állítsa be az Ismétlődés eseményindítót az óra tetején, és ismételje meg 20 percenként, például 9:00, 9:20 és így tovább.

  * A *passzív* letiltott logikai alkalmazás, állítsa be az ismétlődési eseményindító azonos ütemezés, de 10 perccel az óra után kezdődik, és ismételje meg 20 percenként, például 9:10, 9:30 AM, és így tovább.

* A másodlagos helyen állítsa be a [passzív-aktív](#roles) ezeket a logikai alkalmazásokat:

  * A *passzív* letiltott logikai alkalmazás, állítsa be az ismétlődési eseményindító ugyanazon ütemezésszerint, mint az aktív logikai alkalmazás az elsődleges helyen, amely az óra tetején, és ismételje meg 20 percenként, például 9:00, 9:10 AM, és így tovább.

  * Az *aktív* engedélyezett logikai alkalmazás esetében állítsa be az Ismétlődés eseményindítót ugyanarra az ütemezésre, mint az elsődleges helyen lévő passzív logikai alkalmazást, amely az óra után 10 perccel kezdődik, és 20 percenként ismétlődik, például 9:10, 9:20 aM és így tovább.

Most, ha egy zavaró esemény történik az elsődleges helyen, aktiválja a passzív logikai alkalmazást a másik helyen. Így, ha a hiba megtalálása időt vesz igénybe, ez a beállítás korlátozza a késleltetett időszakban nem fogadott ismétlődések számát.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Lekérdezési eseményindító

Rendszeresen ellenőrizze, hogy egy adott szolgáltatásból vagy végpontból elérhető-e új adatok, a logikai alkalmazás használhat egy *lekérdezési* eseményindítót, amely egy rögzített ismétlődési ütemezés alapján ismételten meghívja a szolgáltatást vagy a végpontot. A szolgáltatás vagy a végpont által biztosított adatok a következő típusok valamelyikével rendelkezhetnek:

* Statikus adatok, amelyek az olvasáshoz mindig elérhető adatokat írják le
* Volatilis adatok, amelyek az olvasás után már nem elérhető adatokat írnak le

Ahhoz, hogy ne olvassa el újra ugyanazokat az adatokat, a logikai alkalmazásnak emlékeznie kell arra, hogy mely adatokat olvasták korábban az ügyféloldalon vagy a kiszolgálón, a szolgáltatáson vagy a rendszeroldalon.

* Az ügyféloldali állapottal dolgozó logikai alkalmazások olyan eseményindítókat használnak, amelyek fenntartják az állapotot.

  Például egy eseményindító, amely egy új üzenetet olvas egy e-mail postafiókjából, megköveteli, hogy az eseményindító emlékezzen a legutóbb olvasott üzenetre. Így az eseményindító csak akkor indítja el a logikai alkalmazást, amikor a következő olvasatlan üzenet megérkezik.

* A kiszolgálóval, szolgáltatással vagy rendszeroldali állapottal dolgozó logikai alkalmazások a kiszolgálón, a szolgáltatáson vagy a rendszeroldalon található tulajdonságértékeket vagy -beállításokat használják.

  Például egy lekérdezésalapú eseményindító, amely egy sorot olvas `isRead` be egy adatbázisból, olyan oszlopot kell létrehoznia, amelynek `FALSE`beállítása. Minden alkalommal, amikor az eseményindító beolvas egy sort, `FALSE` a `TRUE`logikai alkalmazás frissíti a sort az `isRead` oszlop módosításával.

  Ez a kiszolgálóoldali megközelítés hasonlóan működik a Service Bus-várólisták vagy témakörök, amelyek sorban állásszemantika, ahol egy eseményindító képes olvasni és zárolni egy üzenetet, miközben a logikai alkalmazás feldolgozza az üzenetet. Amikor a logikai alkalmazás befejezi a feldolgozást, az eseményindító törli az üzenetet a várólistából vagy a témakörből.

Vész-helyreállítási szempontból, amikor beállítja a logikai alkalmazás elsődleges és másodlagos példányait, győződjön meg arról, hogy figyelembe ezeket a viselkedéseket attól függően, hogy a logikai alkalmazás nyomon követi állapotát az ügyféloldalon vagy a kiszolgáló oldalon:

* Egy logikai alkalmazás, amely együttműködik az ügyféloldali állapot, győződjön meg arról, hogy a logikai alkalmazás nem olvassa el ugyanazt az üzenetet egynél többször. Csak egy hely rendelkezhet egy aktív logikai alkalmazáspéldánysal egy adott időpontban. Győződjön meg arról, hogy a logikai alkalmazás példánya az alternatív helyen inaktív vagy le van tiltva, amíg az elsődleges példány átadja a másik helyre.

  Az Office 365 Outlook-eseményindító például fenntartja az ügyféloldali állapotot, és nyomon követi a legutóbb olvasott e-mailek időbélyegét, hogy elkerülje a duplikátumok olvasását.

* A kiszolgálóoldali állapottal működő logikai alkalmazások esetében beállíthatja, hogy a logikai alkalmazáspéldányok [aktív-aktív szerepköröket](#roles) játsszanak, ahol versengő fogyasztóként működnek, vagy [aktív-passzív szerepköröket,](#roles) ahol az alternatív példány megvárja, amíg az elsődleges példány átadja a feladatokat az alternatív helyre.

  Például egy üzenetvárólistából, például egy Azure Service Bus-várólistából történő olvasás kiszolgálóoldali állapotot használ, mert a sorszolgálat zárolja az üzeneteket, hogy megakadályozza, hogy más ügyfelek ugyanazokat az üzeneteket olvassák.

  > [!NOTE]
  > Ha a logikai alkalmazásnak meghatározott sorrendben kell olvasnia az üzeneteket, például egy Service Bus-várólistából, használhatja a versengő fogyasztói mintát, de csak akkor, ha a Service Bus-munkamenetekkel kombinálja, [ *amelyet szekvenciális konvojmintának* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)is neveznek. Ellenkező esetben be kell állítania a logikai alkalmazáspéldányok az aktív-passzív szerepkörök.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Kérelem eseményindító

A **kérelem** eseményindító teszi a logikai alkalmazás hívható más alkalmazások, szolgáltatások és rendszerek, és általában az alábbi képességek biztosításához használt:

* Közvetlen REST API a logikai alkalmazáshoz, amelyet mások hívhatnak

  Például használja a kérelem eseményindító a logikai alkalmazás elindításához, így más logikai alkalmazások hívhatja meg az eseményindító segítségével a **hívási munkafolyamat - Logic Apps** művelet.

* [Webhook](#webhook-trigger) vagy visszahívási mechanizmus a logikai alkalmazáshoz

* A logikai alkalmazás hívásához manuálisan futtatható felhasználói műveletek vagy rutinok, például egy adott feladatot elvégző PowerShell-parancsfájl használatával

Vész-helyreállítási szempontból a kérelem eseményindító egy passzív fogadó, mert a logikai alkalmazás nem végez semmilyen munkát, és megvárja, amíg egy másik szolgáltatás vagy rendszer explicit módon meghívja az eseményindítót. Passzív végpontként az elsődleges és másodlagos példányokat a következő módokon állíthatja be:

* [Aktív-aktív](#roles): Mindkét példány aktívan kezeli a kérelmeket vagy hívásokat. A hívó vagy az útválasztó kiegyenlíti vagy elosztja a forgalmat a példányok között.

* [Aktív-passzív:](#roles)Csak az elsődleges példány aktív, és kezeli az összes munkát, míg a másodlagos példány megvárja, amíg az elsődleges megszakítás vagy hiba. A hívó vagy az útválasztó határozza meg, hogy mikor kell hívni a másodlagos példányt.

Ajánlott architektúraként használhatja az Azure API Management proxyként a logikai alkalmazások, amelyek a kérelem eseményindítók. Az API Management beépített régiók közötti rugalmasságot biztosít, [és képes a forgalmat több végpontra irányítani.](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-eseményindító

A *webhook-eseményindító* lehetővé teszi a logikai alkalmazás számára, hogy előfizessen egy szolgáltatásra egy *visszahívási URL-címet* a szolgáltatásnak. A logikai alkalmazás ezután figyelheti, és megvárja, amíg egy adott esemény történik, hogy a szolgáltatás végpontján. Amikor az esemény bekövetkezik, a szolgáltatás meghívja a webhook-eseményindító a visszahívási URL-címet, amely ezután futtatja a logikai alkalmazást. Ha engedélyezve van, a webhook-eseményindító előfizet a szolgáltatásra. Ha le van tiltva, az eseményindító leiratkozik a szolgáltatásról.

Vész-helyreállítási szempontból állítsa be az elsődleges és másodlagos példányok, amelyek webhook-eseményindítók aktív-passzív szerepkörök lejátszásához, mert csak egy példány nak kell fogadnia eseményeket vagy üzeneteket az előfizetett végpontról.

## <a name="assess-primary-instance-health"></a>Elsődleges példány állapotának felmérése

A vész-helyreállítási stratégia működéséhez a megoldásnak szüksége van az alábbi feladatok végrehajtására:

* [Az elsődleges példány elérhetőségének ellenőrzése](#check-primary-availability)
* [Az elsődleges példány állapotának figyelése](#monitor-primary-health)
* [A másodlagos példány aktiválása](#activate-secondary)

Ez a rész egy olyan megoldást ismertet, amelyet közvetlenül vagy a saját tervezés alapjaként használhat. Az alábbiakban a megoldás magas szintű vizuális áttekintését olvashatja:

![Watchdog logikai alkalmazás létrehozása, amely egy állapot-ellenőrzési logikai alkalmazást figyel az elsődleges helyen](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Az elsődleges példány elérhetőségének ellenőrzése

Annak megállapításához, hogy az elsődleges példány elérhető, futó és képes-e dolgozni, létrehozhat egy "állapot-ellenőrzés" logikai alkalmazást, amely ugyanazon a helyen található, mint az elsődleges példány. Ezután ezt az állapot-ellenőrző alkalmazást egy másik helyről hívhatja fel. Ha az állapot-ellenőrzési alkalmazás sikeresen válaszol, az azure logic apps szolgáltatás alapjául szolgáló infrastruktúra az adott régióban elérhető és működik. Ha az állapot-ellenőrzési alkalmazás nem válaszol, feltételezheti, hogy a hely már nem kifogástalan.

Ehhez a feladathoz hozzon létre egy alapvető állapot-ellenőrzési logikai alkalmazást, amely a következő feladatokat hajtja végre:

1. Hívás fogadása a figyelő alkalmazásból a Kérelem eseményindító használatával.

1. Válaszoljon egy állapottal, amely jelzi, hogy a bejelölt logikai alkalmazás továbbra is működik-e a Válasz művelet használatával.

   > [!IMPORTANT]
   > Az állapot-ellenőrzési logikai alkalmazás nak egy válaszműveletet kell használnia, hogy az alkalmazás szinkron módon reagáljon, ne aszinkron módon.

1. Ha további meghatározásához, hogy az elsődleges hely kifogástalan állapotban van-e, figyelembe veheti bármely más olyan szolgáltatás állapotát, amely ezen a helyen kommunikál a céllogikai alkalmazással. Csak bontsa ki az állapot-ellenőrzési logikai alkalmazást, hogy felmérje az állapotát ezen egyéb szolgáltatások is.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Watchdog logikai alkalmazás létrehozása

Az elsődleges példány állapotának figyeléséhez és az állapot-ellenőrzési logikai alkalmazás hívásához hozzon létre egy "figyelő" logikai alkalmazást egy *másik helyen.* Beállíthatja például a figyelő logikai alkalmazást, hogy ha az állapot-ellenőrzési logika hívása sikertelen, a figyelő riasztást küldhet az operatív csapatnak, hogy kivizsgálhassák a hibát, és miért az elsődleges példány nem válaszol.

> [!IMPORTANT]
> Győződjön meg arról, hogy a figyelő logikai alkalmazása az *elsődleges helytől eltérő helyen*található. Ha a Logic Apps szolgáltatás az elsődleges helyen problémákat tapasztal, a figyelő logikai alkalmazás nem futtathatja.

Ehhez a feladathoz a másodlagos helyen hozzon létre egy figyelő logikai alkalmazást, amely végrehajtja ezeket a feladatokat:

1. Az Ismétlődés eseményindító használatával rögzített vagy ütemezett ismétlődés alapján futtassa.

   Az ismétlődést olyan értékre állíthatja be, amely a helyreállítási idő célkitűzés (RTO) tűréshatára alatt van.

1. Hívja meg az állapot-ellenőrzési logikai alkalmazást az elsődleges helyen a HTTP-művelet használatával, például:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>A másodlagos példány aktiválása

A másodlagos példány automatikus aktiválásához létrehozhat egy logikai alkalmazást, amely meghívja a felügyeleti API-t, például az [Azure Resource Manager-összekötőt](https://docs.microsoft.com/connectors/arm/) a megfelelő logikai alkalmazások aktiválásához a másodlagos helyen. Bonthatja ki a figyelő alkalmazást, hogy hívja meg ezt az aktiválási logikai alkalmazást, miután egy adott számú hiba történt.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Diagnosztikai adatok gyűjtése

Beállíthatja a naplózása a logikai alkalmazás fut, és küldje el az eredményül kapott diagnosztikai adatokat a szolgáltatások, például az Azure Storage, az Azure Event Hubs és az Azure Log Analytics további kezelés és feldolgozás érdekében.

* Ha szeretné használni ezeket az adatokat az Azure Log Analytics, az adatok elérhetővé mind az elsődleges és másodlagos helyeken a logikai alkalmazás **diagnosztikai beállításait,** és elküldi az adatokat több Log Analytics-munkaterületekre. További információ: Az Azure Monitor naplók beállítása és diagnosztikai adatok gyűjtése az Azure Logic Apps.for more information, for [Set up Azure Monitor logs and collect diagnostics data for Azure Logic Apps.](../logic-apps/monitor-logic-apps-log-analytics.md)

* Ha az adatokat az Azure Storage-ba vagy az Azure Event Hubs-ba szeretné küldeni, az adatokat elérhetővé teheti mind az elsődleges, mind a másodlagos helyeken a georedundancia beállításával. További információval a következő cikkek szolgálnak:<p>

  * [Az Azure Blob Storage vész-helyreállítási és fiókfeladat-átvétele](../storage/common/storage-disaster-recovery-guidance.md)
  * [Az Azure Event Hubs geokatasztrófa-helyreállítása](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>További lépések

* [Rugalmasság – áttekintés az Azure-hoz](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Az egyes Azure-szolgáltatások rugalmasságára vonatkozó ellenőrzőlista](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Adatkezelés az Azure rugalmassága érdekében](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Biztonsági mentés és vészhelyreállítás az Azure-alkalmazásokhoz](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Helyreállítás egész régióra kiterjedő szolgáltatáskimaradás esetén](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft szolgáltatásiszint-szerződések (SLOS-ok) az Azure-szolgáltatásokhoz](https://azure.microsoft.com/support/legal/sla/)
