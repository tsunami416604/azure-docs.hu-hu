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
ms.date: 11/29/2018
ms.openlocfilehash: 798b50887bcfdf5b4298c37beb1b9eea8f9abdda
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682197"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Azure virtuális hálózatok az Azure Logic Apps keresztül csatlakozhat egy integrációs service-környezet (ISE)

> [!NOTE]
> Ez a funkció akkor a *privát előzetes verzió*. Hozzáférés igényléséhez [itt csatlakozni a kérelem létrehozása](https://aka.ms/iseprivatepreview).

Forgatókönyvek, ahol a logic apps és az integrációs fiókok kell a hozzáférést egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md), hozzon létre egy [ *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Az ISE-ben olyan dedikált tárolási használó magán- és elkülönített környezet és más tárolt erőforrások elkülönítése a nyilvános vagy *globális* Logic Apps szolgáltatást. Ez a fajta elkülönítés is csökkenti, amelyeket más Azure-bérlőt az alkalmazások teljesítményére hatással. Az ISE *beszúrta* be az Azure virtuális hálózathoz, amely azután telepíti a Logic Apps szolgáltatás a virtuális hálózatban. Amikor létrehoz egy logikai alkalmazásban vagy integrációs fiókot, válassza ki az ISE azok helyétől. A logikai alkalmazás vagy az integrációs fiók majd közvetlenül hozzáférhet a erőforrások, például a virtuális gépek (VM), kiszolgálók, rendszerek és szolgáltatások, a virtuális hálózaton. 

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Állítsa be az Azure virtuális hálózat engedélyeit, hogy a privát Logic Apps-példány hozzáférhessen a virtuális hálózat.

* Az integrációs service-környezet (ISE) létrehozása. 

* Hozzon létre egy logikai alkalmazást, amely képes futni az ISE-ben. 

* A logic apps integrációs fiók létrehozása az ISE-ben.

Integrációs service Environment-környezetekkel kapcsolatos további információkért lásd: [Azure Logic Apps az Azure Virtual Network-erőforrásokhoz való hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). Ha nem rendelkezik virtuális hálózattal, megtudhatja, hogyan [egy Azure virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md). 

* Az Azure virtuális hálózat, a logic apps közvetlen hozzáférést biztosít a [állítsa be a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel](#vnet-access) , a Logic Apps szolgáltatás rendelkezik a virtuális hálózat eléréséhez szükséges engedélyekkel. 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Virtuális hálózati engedélyeinek beállítása

Amikor létrehoz egy integrációs service-környezet (ISE), hol válassza ki Azure-beli virtuális hálózathoz, *beszúrása* a környezetben. Azonban a környezet betöltése a virtuális hálózat kiválasztása előtt be kell állítania szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel a virtuális hálózaton. Engedélyek beállításához, ezek a szerepkörök hozzárendelése az Azure Logic Apps szolgáltatás:

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki a virtuális hálózatot. 

1. Válassza ki a virtuális hálózat menü, **hozzáférés-vezérlés (IAM)**. 

1. A **hozzáférés-vezérlés (IAM)**, válassza a **Hozzáadás**. 

   ![Szerepkörök hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Az a **szerepkör-hozzárendelés hozzáadása** leírtak szerint ebben a lépésben a táblázat az Azure Logic Apps szolgáltatás minden szerepkör beállítása panelen. Válasszon **mentése** minden szerepkör befejezése után.

   ![Szerepkör-hozzárendelés hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   | Szerepkör | Hozzáférés hozzárendelése a következőhöz: | Válassza ezt: | 
   |------|------------------|--------|
   | **Hálózati közreműködő** | **Az Azure AD-felhasználó, csoport vagy alkalmazás** | Adja meg **Azure Logic Apps**. Miután a tagok listája megjelenik, válassza ki ugyanazt az értéket. <p>**Tipp**: Ha ez a szolgáltatás nem találja, adja meg a Logic Apps szolgáltatás Alkalmazásazonosító: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Klasszikus Közreműködője** | **Az Azure AD-felhasználó, csoport vagy alkalmazás** | Adja meg **Azure Logic Apps**. Miután a tagok listája megjelenik, válassza ki ugyanazt az értéket. <p>**Tipp**: Ha ez a szolgáltatás nem találja, adja meg a Logic Apps szolgáltatás Alkalmazásazonosító: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

További információkért lásd: [virtuális hálózati hozzáférés engedélyeinek](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

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

   | Tulajdonság | Szükséges | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés-neve*> | Az Azure-előfizetés a környezet használata | 
   | **Erőforráscsoport** | Igen | <*Az Azure-erőforrás-csoport-neve*> | Az Azure erőforráscsoport, ahol szeretné létrehozni a környezetet |
   | **Integráció Service-környezet neve** | Igen | <*környezet neve*> | A környezet nevét | 
   | **Hely** | Igen | <*Az Azure-adatközpont-régió*> | Az Azure-adatközpontrégiót használhatják az üzembe helyezés a környezet | 
   | **Kapacitás** | Igen | 0, 1, 2, 3 | Az ISE-erőforrás használandó feldolgozási egységek száma | 
   | **Virtuális hálózat** | Igen | <*Az Azure-virtuális hálózat neve*> | Az Azure virtuális hálózat, ahol szeretné a környezet betöltése, hogy a logic apps, a környezetben hozzáférhessen a virtuális hálózat. Ha nem rendelkezik olyan hálózattal, létrehozhat egy itt. <p>**Fontos**: is *csak* észrevegye hajtható végre, ha a hoz létre az ISE-ben. Azonban ez a kapcsolat létrehozásához, győződjön meg arról, hogy már [beállítása szerepköralapú hozzáférés-vezérlés a virtuális hálózat az Azure Logic Apps](#vnet-access). | 
   | **Alhálózatok** | Igen | <*IP-címtartomány*> | Az ISE-ben szükséges négy *üres* alhálózatok, amely bármely szolgáltatás delegálási nem rendelkezik, és -erőforrások létrehozását a környezetében használt. Minden alhálózati ezeknek a feltételeknek kell megfelelnie: <p>– Használja az [Classless Inter-Domain Routing (CIDR) formátum](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). <br>-A-B osztály cím terület szükséges. <br>– Egy nevet, amely nem kezdődik számmal vagy kötőjel tartozik. <br>-Tartalmaz egy `/27`, például minden egyes alhálózat adja meg egy 32 bites címtartomány: `10.0.0.0/27`, `10.0.0.32/27`, `10.0.0.64/27`, és `10.0.0.96/27`. <br>-Nem léteznie kell a kiválasztott virtuális hálózaton, sem bármely más magánhálózati IP-címek amikor csatlakozik a virtuális hálózat azonos címtartománya. <br>-Kell megadni. <p><p>**Fontos**: Ön *nem módosítható* ezen IP-címtartományok a környezet létrehozása után. |
   |||||

1. Miután az Azure sikeresen ellenőrzi az ISE-adatokat, válassza ki a **létrehozás**, például:

   ![Sikeres ellenőrzés után válassza a "Létrehozás"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure elindul, a környezet üzembe helyezéséhez, de ez a folyamat eltarthat *akár két órát* befejezése előtt. 
   Ellenőrizze a telepítés állapota, az Azure eszköztárában válassza az értesítések ikont, amely megnyitja az értesítési panelen.

   ![Üzembe helyezési állapot ellenőrzése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Központi telepítés sikeres befejezését követően az Azure ezt az értesítést jelenít meg:

   ![A telepítés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. A környezet megtekintéséhez válassza **erőforrás megnyitása** , ha az Azure nem automatikusan nyissa meg a környezet üzembe helyezés befejezése után.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Logikai alkalmazás létrehozása – ISE-ben

Az integrációs service-környezet (ISE) használó logikai alkalmazások létrehozása, kövesse a [Logic Apps-alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) , de a következő eltérésekkel: 

* A logikai alkalmazás létrehozásakor válassza ki az ISE-ben, nem pedig egy Azure-régióban, az a **hely** listájához a **integrációs service-környezetek** részben, például:

  ![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Az azonos beépített eseményindítók és műveletek, például a HTTP-n a azonos ISE-ben, a logikai alkalmazás futtatásához, amelyek is használhatja. Az összekötők a **ISE** címkézését is az azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Miután az ISE szúr be egy Azure-beli virtuális hálózatban, az ISE-ben a logic apps közvetlenül hozzáférhet a virtuális hálózatban lévő erőforrásokra. A helyszíni rendszerek egy virtuális hálózatban, amely kapcsolódik az ISE-ben a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

  * ISE-ben, hogy a rendszer, például az SQL Server-összekötő
  * HTTP-művelet 
  * Egyéni összekötő

  A helyszíni rendszerek, amelyek nem a virtuális hálózat, vagy nem rendelkezik ISE összekötők, először [beállítása és használata a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Integrációs fiók létrehozása – ISE-ben

A logic apps integrációs service-környezet (ISE) az integrációs fiók használatához integrációs fiókhoz kell használnia a *ugyanabban a környezetben* a logic Apps. A Logic apps egy ISE-ben csak integrációs fiókok ugyanazt az ISE-ben is lehet hivatkozni. 

Hozzon létre egy ISE használó integrációs fiókból, hajtsa végre a szokásos lépéseit [integrációs fiókok létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kivételével a **hely** tulajdonság, amely most már tartalmazza a ISEs alatt  **Integráció service-környezetek** együtt elérhető régiók. Válassza ki például az ISE-ben, nem pedig egy régió:

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
