---
title: Azure virtuális hálózatok az Azure Logic Apps keresztül csatlakozhat egy integrációs service-környezet (ISE)
description: Hozzon létre egy integrációs service-környezet (ISE) logic apps és az integrációs fiókok hozzáférhet az Azure virtuális hálózatok (Vnetek), privát és nyilvános vagy "globális" Azure elkülönítve maradva háríthatják
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: c31d260c99707f4231a6833479517b9b69575d55
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778909"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás az Azure virtuális hálózatok az Azure Logic Apps integrációs service-környezet (ISE) használatával

> [!NOTE]
> Ez a funkció akkor a [ *nyilvános előzetes verzióban*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Forgatókönyvek, ahol a logic apps és az integrációs fiókok kell a hozzáférést egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md), hozzon létre egy [ *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Az ISE-ben egy dedikált tárolási használó magán- és elkülönített környezet és más erőforrások elkülönítve a nyilvános vagy "globális" Logic Apps szolgáltatást. Ez a fajta elkülönítés is csökkenti, amelyeket más Azure-bérlőt az alkalmazások teljesítményére hatással. Az ISE *beszúrta* be az Azure virtuális hálózathoz, amely azután telepíti a Logic Apps szolgáltatás a virtuális hálózatban. Amikor létrehoz egy logikai alkalmazásban vagy integrációs fiókot, válassza ki az ISE azok helyétől. A logikai alkalmazás vagy az integrációs fiók majd közvetlenül hozzáférhet a erőforrások, például a virtuális gépek (VM), kiszolgálók, rendszerek és szolgáltatások, a virtuális hálózaton.

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Állítsa be az Azure virtuális hálózat található portokat Forgalom utazhat az integrációs service-környezet (ISE) keresztül, a virtuális hálózat alhálózatai között.

* Az integrációs service-környezet (ISE) létrehozása.

* Hozzon létre egy logikai alkalmazást, amely képes futni az ISE-ben.

* A logic apps integrációs fiók létrehozása az ISE-ben.

Integrációs service Environment-környezetekkel kapcsolatos további információkért lásd: [Azure Logic Apps az Azure Virtual Network-erőforrásokhoz való hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

  > [!IMPORTANT]
  > A Logic apps beépített műveleteket és az ISE-ben futó összekötők használja egy másik díjszabási csomagot, nem a fogyasztás alapú díjszabással. További információkért lásd: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md).

* Egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). Ha nem rendelkezik virtuális hálózattal, megtudhatja, hogyan [egy Azure virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md). 

  * A virtuális hálózatnak rendelkeznie kell négy *üres* üzembe helyezéséhez és erőforrások létrehozása az ISE-ben alhálózat. Ezen alhálózatok előre hozhat létre, vagy megvárhatja, amíg nem hoz létre az ISE-ben, ahol létre alhálózatok egyszerre. Tudjon meg többet [alhálózati követelmények](#create-subnet).

  * Győződjön meg arról, hogy a virtuális hálózat [elérhetővé teszi ezeket a portokat](#ports) így megfelelően működik-e az ISE-ben, és elérhető marad.

* Egy vagy több egyéni DNS-kiszolgálók üzembe helyezése az Azure virtuális hálózat használandó [beállítása az adott kiszolgálókon, ez az útmutató következő](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) az ISE-ben a virtuális hálózat üzembe helyezése előtt. Ellenkező esetben minden alkalommal, amikor módosítja a DNS-kiszolgáló is újraindítani az ISE-ben, amely egy olyan funkció, amely az ISE-ben nyilvános előzetes verzióban érhető el.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Állítsa be a hálózati portok

Megfelelően működjön, és elérhető-e, az integráció service-környezet (ISE) kell rendelkeznie az adott portokon a virtuális hálózaton rendelkezésre álló. Ezeket a portokat bármelyike nem érhető el, előfordulhat, hogy az ISE-ben, amelyek működése leáll, ellenkező esetben férhet hozzá. Az ISE-ben a virtuális hálózatban való használatakor gyakori telepítési problémát tapasztalja egy vagy több letiltott portot. Az ISE-ben és a cél rendszer közötti kapcsolatok esetén használja az összekötő is szükség lehet a saját port követelményei. Például ha az FTP-összekötő használatával kommunikálnak az FTP-rendszerek, győződjön meg arról, a portot használja, hogy elérhető legyen-e az FTP-rendszer 21-es porton, a Parancsküldés, például.

A bejövő és kimenő forgalom szabályozása, amelyen központi telepítését az ISE-ben a virtuális hálózat alhálózatainak között, beállíthatja [hálózati biztonsági csoportok](../virtual-network/security-overview.md) az ezekhez az alhálózatokhoz tanulással [közötti hálózati forgalom szűrése alhálózatok](../virtual-network/tutorial-filter-network-traffic.md). Ezek a táblázatok ismertetik a portokat a virtuális hálózat, amely az ISE-ben, és ahol azokat a portokat használja beolvasása. A [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) IP-címelőtagokat, amelyek segítenek a minimálisra összetettségét, amikor a biztonsági szabályok létrehozása egy csoportját jelöli. 

> [!IMPORTANT]
> A belső kommunikáció belül az alhálózatokra az ISE-ben szükséges ezekhez az alhálózatokhoz belül minden portok megnyitását. 

| Cél | Irány | Portok | Forrás-szolgáltatáscímke | Cél szolgáltatáscímkéje | Megjegyzések |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Az Azure Logic Apps-kommunikációt | Kimenő | 80 & 443 | VIRTUAL_NETWORK | INTERNET | A külső szolgáltatás, amellyel kommunikál a Logic Apps szolgáltatás függ, hogy a port |
| Azure Active Directory | Kimenő | 80 & 443 | VIRTUAL_NETWORK | AzureActiveDirectory | |
| Az Azure Storage-függőségek | Kimenő | 80 & 443 | VIRTUAL_NETWORK | Storage | |
| Intersubnet kommunikáció | A bejövő és kimenő | 80 & 443 | VIRTUAL_NETWORK | VIRTUAL_NETWORK | Az alhálózatok közötti kommunikációhoz |
| Az Azure Logic Apps-kommunikációt | Bejövő | 443 | INTERNET  | VIRTUAL_NETWORK | A számítógép vagy szolgáltatás, amely meghívja ezt bármilyen kérelem típusú trigger vagy a webhookot, amely létezik a logikai alkalmazás az IP-címe. Bezárásával vagy blokkolja ezt a portot megakadályozza, hogy a kérelemtriggerekkel rendelkező logikai alkalmazások HTTP-hívások.  |
| Logikai alkalmazás futtatási előzmények | Bejövő | 443 | INTERNET  | VIRTUAL_NETWORK | A számítógép, amelyen megtekintheti a logikai alkalmazás IP-címét a futtatási előzmények. Bezárásával vagy blokkolja ezt a portot nem akadályozza meg a futtatási előzmények megtekintése, bár nem tekintheti meg a bemenetek és kimenetek, amelyek az egyes lépések futtatási előzmények. |
| Kapcsolat kezelése | Kimenő | 443 | VIRTUAL_NETWORK  | INTERNET | |
| Diagnosztikai naplók és mérőszámok közzététele | Kimenő | 443 | VIRTUAL_NETWORK  | AzureMonitor | |
| Logic Apps Designer – dinamikus tulajdonságai | Bejövő | 454 | INTERNET  | VIRTUAL_NETWORK | Kérelmek származhatnak a Logic Apps [végpontot bejövő IP-címeket az adott régióban](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| App Service Management-függőség | Bejövő | 454 & 455 | AppServiceManagement | VIRTUAL_NETWORK | |
| Összekötő üzembe helyezés | Bejövő | 454 & 3443 | INTERNET  | VIRTUAL_NETWORK | Üzembe helyezése és összekötők frissítése szükséges. Bezárásával vagy blokkolja ezt a portot hatására ISE központi telepítés sikertelen lesz, és megakadályozza, hogy a összekötő frissítéseket és javításokat. |
| Az Azure SQL-függőség | Kimenő | 1433 | VIRTUAL_NETWORK | SQL |
| Azure Resource Health | Kimenő | 1886 | VIRTUAL_NETWORK | INTERNET | A Resource Health-közzététel állapota |
| Az API Management - felügyeleti végpont | Bejövő | 3443 | APIManagement  | VIRTUAL_NETWORK | |
| Eseményközpont-szabályzat és a monitorozási ügynök a napló függőséget | Kimenő | 5672 | VIRTUAL_NETWORK  | EventHub | |
| Az Azure Cache elérése a Redis-példány között szerepkör példányai | Bejövő <br>Kimenő | 6379-6383 | VIRTUAL_NETWORK  | VIRTUAL_NETWORK | Ezenkívül az ISE-ben használható az Azure Cache a Redis, meg kell nyitnia ezeket [kimenő és bejövő portokat a redis Cache – gyakori kérdések az Azure Cache ismertetett](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Bejövő | * | AZURE_LOAD_BALANCER | VIRTUAL_NETWORK |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Hozzon létre az ISE-ben

Az integrációs service-környezet (ISE) létrehozásához kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **erőforrás létrehozása**.

   ![Új erőforrás létrehozása](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. A keresőmezőbe írja be szűrőként "integrációs szolgáltatás környezet".
Az eredmények listájában válassza ki a **integrációs Service-környezet (előzetes verzió)**, és válassza a **létrehozás**.

   ![Válassza ki a "Integrációs szolgáltatás környezet"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Válassza a "Create"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. A környezetnek meg az alábbi adatokat, és válassza a **felülvizsgálat + létrehozása**, például:

   ![Adja meg a környezet részletei](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés-neve*> | Az Azure-előfizetés a környezet használata |
   | **Erőforráscsoport** | Igen | <*Azure-resource-group-name*> | Az Azure erőforráscsoport, ahol szeretné létrehozni a környezetet |
   | **Integráció Service-környezet neve** | Igen | <*environment-name*> | A környezet nevét |
   | **Hely** | Igen | <*Azure-datacenter-region*> | Az Azure-adatközpontrégiót használhatják az üzembe helyezés a környezet |
   | **Ha extra kapacitásra** | Igen | 0, 1, 2, 3 | Az ISE-erőforrás használandó feldolgozási egységek száma. Létrehozása után adja hozzá a kapacitás, lásd: [növelhetjük a Kapacitásunkat](#add-capacity). |
   | **Virtuális hálózat** | Igen | <*Azure-virtual-network-name*> | Az Azure virtuális hálózat, ahol szeretné a környezet betöltése, hogy a logic apps, a környezetben hozzáférhessen a virtuális hálózat. Ha nem rendelkezik olyan hálózattal, létrehozhat egy itt. <p>**Fontos**: Is *csak* észrevegye hajtható végre, ha a hoz létre az ISE-ben. Azonban ez a kapcsolat létrehozásához, győződjön meg arról, hogy már [beállítása szerepköralapú hozzáférés-vezérlés a virtuális hálózat az Azure Logic Apps](#vnet-access). |
   | **Alhálózatok** | Igen | <*subnet-resource-list*> | Az ISE-ben szükséges négy *üres* alhálózatok a környezetében az erőforrások létrehozásához. Minden egyes alhálózat létrehozásához [a táblázat alatti lépéseket követve](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Alhálózat létrehozása**

   Az ISE-ben szükséges négy *üres* alhálózatok, amelyek *nem meghatalmazott* bármely szolgáltatás a környezetében az erőforrások létrehozásához. 
   Ön *nem módosítható* a környezet létrehozása után ezeket alhálózati cím. Minden alhálózati ezeknek a feltételeknek kell megfelelnie:

   * Olyan nevet, amely nem kezdődik számmal vagy kötőjel használ.

   * Használja a [Classless Inter-Domain Routing (CIDR) formátum](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) és a B osztály címtér.

   * Használja legalább egy `/27` címét a lemezterület-minden egyes alhálózatnak rendelkeznie kell 32 címet, mert a *minimális*. Példa:

     * `10.0.0.0/27` 32-címmel rendelkezik, mert az 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32.

     * `10.0.0.0/24` 256-címmel rendelkezik, mert az 2<sup>(32-24)</sup> 2<sup>8</sup> vagy 256.

     * `10.0.0.0/28` csak 16-címmel rendelkezik, és túl kicsi mert 2<sup>(32-28)</sup> 2<sup>4</sup> vagy 16.

     Címek kiszámítása kapcsolatos további információkért lásd: [IPv4 CIDR-blokkok](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   1. Alatt a **alhálózatok** menüben válassza ki **kezelés alhálózati konfigurációt**.

      ![Alhálózati konfiguráció kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Az a **alhálózatok** panelen válassza a **alhálózati**.

      ![Alhálózat hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Az a **alhálózat hozzáadása** panelen adja meg ezt az információt.

      * **Név**: Az alhálózat nevét
      * **Címtartomány (CIDR-blokk)**: Az alhálózati címtartományt a virtuális hálózat és a CIDR formátumban

      ![Adja hozzá az alhálózati adatokat](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Ha elkészült, válassza ki a **OK**.

   1. Ismételje meg ezeket a lépéseket három további alhálózatokat.

1. Miután az Azure sikeresen ellenőrzi az ISE-adatokat, válassza ki a **létrehozás**, például:

   ![Sikeres ellenőrzés után válassza a "Létrehozás"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure elkezdi a környezetben, de ez a folyamat üzembe helyezéséhez *előfordulhat, hogy* befejezése előtt két órán belül. 
   Ellenőrizze a telepítés állapota, az Azure eszköztárában válassza az értesítések ikont, amely megnyitja az értesítési panelen.

   ![Üzembe helyezési állapot ellenőrzése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha a központi telepítés sikeres befejezését követően az Azure ezt az értesítést jelenít meg:

   ![A telepítés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Ha üzembe helyezése nem sikerül, vagy törölje az ISE-ben, Azure *előfordulhat, hogy* az alhálózatok kibocsátása előtt egy órát is igénybe vehet. Tehát akkor előfordulhat, hogy várnia kell, ezekhez az alhálózatokhoz egy másik ISE-ben újrahasznosítása előtt.

1. A környezet megtekintéséhez válassza **erőforrás megnyitása** , ha az Azure nem automatikusan nyissa meg a környezet üzembe helyezés befejezése után.  

<a name="add-capacity"></a>

### <a name="add-capacity"></a>Adja hozzá a kapacitás

Az ISE alapegység javította a kapacitást, így a kapacitás növelése érdekében van szükség, ha több skálázási egység is hozzáadhat. Választhat az automatikus méretezés, teljesítmény-mérőszámon alapuló, vagy egy meghatározott feldolgozási egységek száma alapján. Ha úgy dönt, hogy az automatikus skálázás metrika alapján, különböző feltételek közül választhat, és adja meg a feltételeknek megfelelő küszöbértéket feltételeit.

1. Az Azure Portalon keresse meg az ISE-ben.

1. Az ISE-ben, az ISE főmenü, teljesítmény-mérőszámok megtekintéséhez válassza ki a **áttekintése**.

1. Alatt állíthatja be az automatikus skálázás, **beállítások**válassza **horizontális felskálázása**. Az a **konfigurálása** lapra, majd **automatikus skálázás engedélyezése**.

1. Az a **alapértelmezett** válassza vagy **skálázás metrika alapján** vagy **skálázás adott példányszámra**.

1. Ha úgy dönt, hogy példány-alapú, adja meg a feldolgozási egységek száma 0 és 3 közötti szélsőértékeket is beleértve. Egyéb, a metrika-alapú, kövesse az alábbi lépéseket:

   1. Az a **alapértelmezett** válassza **egy szabály hozzáadásához**.

   1. Az a **skálázási szabályhoz** panelen állítsa be a feltételeket és a művelet érvénybe a szabály aktiválásakor.

   1. Ha elkészült, válassza ki a **Hozzáadás**.

1. Ha elkészült, ne felejtse el menteni a módosításokat.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Logikai alkalmazás létrehozása – ISE-ben

Az integrációs service-környezet (ISE) használó logikai alkalmazások létrehozása, kövesse a [Logic Apps-alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) , de a következő eltérésekkel: 

* Létrehozásakor a logikai alkalmazást, a a **hely** tulajdonság, válassza ki az ISE-ben a a **integrációs service-környezetek** részben, például:

  ![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Azonos beépített eseményindítók és műveletek, például a HTTP-n, a azonos ISE-ben, a logikai alkalmazás futtatásához, amelyek is használhatja. Az összekötők a **ISE** címkézését is az azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Miután az ISE szúr be egy Azure-beli virtuális hálózatban, az ISE-ben a logic apps közvetlenül hozzáférhet a virtuális hálózatban lévő erőforrásokra. Egy virtuális hálózathoz csatlakozó helyszíni rendszerekhez behelyezése egy ISE-ben, e hálózat, a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

  * ISE-ben, hogy a rendszer, például az SQL Server-összekötő
  
  * HTTP-művelet 
  
  * Egyéni összekötő

  A helyszíni rendszerek, amelyek nem a virtuális hálózat, vagy nem rendelkezik ISE összekötők, először [beállítása a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Integrációs fiók létrehozása – ISE-ben

A logic apps integrációs service-környezet (ISE) az integrációs fiók használatához integrációs fiókhoz kell használnia a *ugyanabban a környezetben* a logic Apps. A Logic apps egy ISE-ben csak integrációs fiókok ugyanazt az ISE-ben is lehet hivatkozni. 

Hozzon létre egy ISE használó integrációs fiókból, kövesse a [integrációs fiókok létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kivételével a **hely** tulajdonság, a **integrációs service-környezetek**  szakasz jelenik meg. Ehelyett válassza ki az ISE-ben, nem pedig a régiót, például:

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
