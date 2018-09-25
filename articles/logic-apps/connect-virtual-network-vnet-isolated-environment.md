---
title: Csatlakozás az Azure Logic Apps Azure virtuális hálózatok
description: Azure-beli virtuális hálózatok (Vnetek) eléréséhez az Azure Logic Apps, hozhat létre privát, dedikált és elkülönített integrációs service-környezetek, amelyek fenntartják a logic apps és más erőforrások elkülönítése "globális" vagy a nyilvános Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b1a75c140376c1e2e2fdfdcd1581978301ab32f1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996468"
---
# <a name="create-isolated-environments-to-access-azure-virtual-networks-vnets-from-azure-logic-apps"></a>Azure-beli virtuális hálózatok (Vnetek) eléréséhez az Azure Logic Apps elkülönített környezet létrehozása

> [!NOTE]
> Ez a funkció akkor a *privát előzetes verzió*. Hozzáférés igényléséhez [itt csatlakozni a kérelem létrehozása](https://aka.ms/iseprivatepreview).

Az integrációs forgatókönyvek, ahol a logic apps és az integrációs fiókok kell a hozzáférést egy [az Azure virtual network (VNET)](../virtual-network/virtual-networks-overview.md), hozhat létre egy [ *integrációs szolgáltatás környezet* (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , amely összekapcsolja a virtuális hálózathoz, és a Logic Apps szolgáltatást telepíti, a virtuális hálózat. Logic apps és az integrációs fiókok létrehozásakor válassza ki az ISE-ben, azok helyétől. A logic apps és az integrációs fiókok majd közvetlenül hozzáférhet erőforrások, például a virtuális gépek (VM), kiszolgálók, rendszerek és szolgáltatások, a virtuális Hálózatban található. 

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Az ISE-ben olyan dedikált tárolási és más erőforrások, amelyek nyilvános önállóan léteznek használó magán- és elkülönített környezet vagy *globális* Logic Apps szolgáltatást. Ez a fajta elkülönítés is csökkentheti a más Azure-bérlőt az alkalmazások teljesítményére lehet hatással. 

Ez a cikk ezeket a műveleteket mutatja be:

* Állítsa be az Azure virtuális hálózat engedélyeit, hogy a privát Logic Apps-példány hozzáférhessen a virtuális hálózat.

* Az integrációs service-környezet (ISE) létrehozása. 

* Hozzon létre egy logikai alkalmazást, amely képes futni az ISE-ben. 

* A logic apps integrációs fiók létrehozása az ISE-ben.

Integrációs service Environment-környezetekkel kapcsolatos további információkért lásd: [elkülönített Azure Logic Apps az Azure Virtual Network (VNET) erőforrásokhoz való hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Ha egy Azure virtuális hálózat nem rendelkezik, megtudhatja, hogyan [egy Azure virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > A környezet létrehozása már nincs szüksége egy Azure virtuális Hálózatot, amíg is *csak* válassza ki a virtuális hálózat, a környezet társ a környezet létrehozásakor. 

* Az Azure virtuális hálózathoz közvetlen hozzáférést adhat a logic apps [állítsa be a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel](#vnet-access) , a Logic Apps szolgáltatás rendelkezik a virtuális hálózat eléréséhez szükséges engedélyekkel. 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-up-vnet-permissions"></a>Virtuális hálózat engedélyek beállítása

Az integrációs service-környezet létrehozásakor választhat legyen egy Azure virtuális hálózaton (VNET) egy *társ* környezete számára. Azonban a főadatbázisról csak ebben a lépésben vagy *társviszony-létesítés*, a környezet létrehozásakor. Ez a kapcsolat lehetővé teszi, hogy a Logic Apps szolgáltatást közvetlenül csatlakozhat a virtuális Hálózatban található erőforrások és a környezet hozzáférést biztosít azokhoz az erőforrásokhoz. 

A virtuális hálózat kiválasztása előtt szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel kell állítania a virtuális hálózathoz. Ez a feladat végrehajtásához az Azure Logic Apps szolgáltatás szerepkörökhöz kell rendelni.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki a virtuális hálózathoz. Válassza ki a virtuális hálózat menü, **hozzáférés-vezérlés (IAM)**. 

1. A **hozzáférés-vezérlés**válassza **szerepkör-hozzárendelés** Ha még nem lenne kiválasztva. Az a **szerepkör-hozzárendelés** eszköztárán válassza a **Hozzáadás**. 

   ![Adja hozzá a szerepkör-hozzárendelések](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Az a **engedélyek hozzáadása** panelen a táblázatban az Azure Logic Apps szolgáltatás minden szerepkör beállítása. Válasszon **mentése** minden szerepkör befejezése után:

   | Szerepkör | Hozzáférés hozzárendelése a következőhöz: | Válassza ezt: | 
   |------|------------------|--------|
   | **Hálózati közreműködő** | **Az Azure AD-felhasználó, csoport vagy alkalmazás** | Adja meg **Azure Logic Apps**. Miután a tagok listája megjelenik, válassza ki ugyanazt az értéket. <p>**Tipp**: Ha ez a szolgáltatás nem találja, adja meg a Logic Apps szolgáltatás Alkalmazásazonosító: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Klasszikus Közreműködője** | **Az Azure AD-felhasználó, csoport vagy alkalmazás** | Adja meg **Azure Logic Apps**. Miután a tagok listája megjelenik, válassza ki ugyanazt az értéket. <p>**Tipp**: Ha ez a szolgáltatás nem találja, adja meg a Logic Apps szolgáltatás Alkalmazásazonosító: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Példa:

   ![Engedélyek hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

A társviszony-létesítéshez szükséges szerepkör engedélyeivel kapcsolatos további információkért lásd: a [engedélyek létrehozása, módosítása, témakör vagy egy virtuális hálózati társviszony törlése](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Hozzon létre az ISE-ben

Az integrációs service-környezet (ISE) létrehozásához kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **erőforrás létrehozása**.

   ![Új erőforrás létrehozása](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. A keresőmezőbe írja be szűrőként "integrációs szolgáltatás környezet".
Az eredmények listájában válassza ki a **integrációs Service-környezet (előzetes verzió)**, és válassza a **létrehozás**.

   ![Válassza ki a "Integrációs szolgáltatás környezet"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Válassza a "Create"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. A környezetnek meg az alábbi adatokat:

   ![Adja meg a környezet részletei](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Szükséges | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Name (Név)** | Igen | <*környezet neve*> | A környezet nevét | 
   | **Előfizetés** | Igen | <*Az Azure-előfizetés-neve*> | Az Azure-előfizetés a környezet használata | 
   | **Erőforráscsoport** | Igen | <*Az Azure-erőforrás-csoport-neve*> | Az Azure erőforráscsoport, ahol szeretné létrehozni a környezetet |
   | **Hely** | Igen | <*Az Azure-adatközpont-régió*> | Az Azure-adatközpontrégiót használhatják a környezettel kapcsolatos információk tárolására. |
   | **A társ virtuális hálózat** | Nem | <*Az Azure-VNET-name*> | Az Azure virtuális hálózat (VNET), a környezet társítása egy *társ* , a logic apps, a környezetben férhet hozzá a virtuális hálózathoz. Ez a kapcsolat létrehozása előtt győződjön meg arról, hogy már [beállítása szerepköralapú hozzáférés-vezérlés a virtuális hálózat az Azure Logic Apps](#vnet-access). <p>**Fontos**: Bár a virtuális hálózat nem szükséges, kiválaszthatja a virtuális hálózat *csak* a környezet létrehozásakor. | 
   | **Társviszony-létesítési neve** | Igen, egy kijelölt virtuális hálózattal | <*társviszony-létesítési névnek*> | A társ-kapcsolat nevét | 
   | **Virtuális hálózat IP-címtartomány** | Igen, egy kijelölt virtuális hálózattal | <*IP-címtartomány*> | Az erőforrások létrehozásához a környezetében használt IP-címtartományt. Ezt a tartományt kell használnia a [Classless Inter-Domain Routing (CIDR) formátum](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), például 10.0.0.1/16, és a B osztály cím terület szükséges. A tartomány nem létezhet címterén belül, a kiválasztott virtuális hálózat a **társ virtuális hálózat** tulajdonságot, és nem lévő bármilyen egyéb privát IP-címek ahol társ csatlakozik a hálózathoz, vagy a társviszony-létesítés vagy átjárók segítségével. <p><p>**Fontos**: Ön *nem módosítható* e címtartomány a környezet létrehozása után. |
   |||||
   
1. Ha elkészült, kattintson a **Létrehozás** gombra. 

   Azure elindul, a környezet üzembe helyezéséhez, de ez a folyamat eltarthat *akár két órát* befejezése előtt. 
   Ellenőrizze a telepítés állapota, az Azure eszköztárában válassza az értesítések ikont, amely megnyitja az értesítési panelen.

   ![Üzembe helyezési állapot ellenőrzése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Központi telepítés sikeres befejezését követően az Azure ezt az értesítést jelenít meg:

   ![A telepítés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. A környezet megtekintéséhez válassza **erőforrás megnyitása** , ha az Azure nem automatikusan nyissa meg a környezet üzembe helyezés befejezése után.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Logikai alkalmazás létrehozása – ISE-ben

Az integrációs service-környezet (ISE) használó logikai alkalmazások létrehozásához kövesse a szokásos lépéseit [Logic Apps-alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) , de ezek a különbségek és szempontok: 

* A logikai alkalmazás létrehozásakor a **hely** tulajdonságlisták alatt a ISEs **integrációs service-környezetek** együtt elérhető régiók. Válassza ki például az ISE-ben, nem pedig egy régió:

  ![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Az azonos built-ins, például a HTTP-triggert vagy műveletet, a szülő logikai alkalmazással azonos ISE-ben futó is használhatja. Az összekötők a **ISE** címkézését is futtassa a szülő logikai alkalmazással azonos ISE-ben. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Ha korábban már beállított egy Azure virtuális hálózattal, társ az ISE-ben, a logic apps az ISE-ben közvetlenül hozzáférhet a virtuális Hálózatban található erőforrások. A helyszíni rendszerek egy virtuális hálózatban, amely kapcsolódik az ISE-ben a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

  * ISE-ben, hogy a rendszer, például az SQL Server-összekötő

  * HTTP-művelet 

  * Egyéni összekötő

  Helyszíni rendszerekhez, amely nem a virtuális hálózathoz, vagy nem rendelkezik az ISE-összekötők, továbbra is a kapcsolódás után, [beállítása és használata a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Integrációs fiók létrehozása – ISE-ben

A logic apps integrációs service-környezet (ISE) az integrációs fiók használatához integrációs fiókhoz kell használnia a *ugyanabban a környezetben* a logic Apps. A Logic apps egy ISE-ben csak integrációs fiókok ugyanazt az ISE-ben is lehet hivatkozni. 

Hozzon létre egy ISE használó integrációs fiókból, hajtsa végre a szokásos lépéseit [integrációs fiókok létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kivételével a **hely** tulajdonság, amely most már tartalmazza a ISEs alatt  **Integráció service-környezetek** együtt elérhető régiók. Válassza ki például az ISE-ben, nem pedig egy régió:

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="http://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
