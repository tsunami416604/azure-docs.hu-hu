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
ms.date: 12/06/2018
ms.openlocfilehash: 41ba0816dde63bc611dcb5be544609b88dfe9158
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052643"
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

1. A **hozzáférés-vezérlés (IAM)**, válassza a **szerepkör-hozzárendelés hozzáadása**. 

   ![Szerepkörök hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Az a **szerepkör-hozzárendelés hozzáadása** ablaktáblán, a szükséges szerepkör hozzáadása az Azure Logic Apps szolgáltatásba leírtak szerint. 

   1. A **szerepkör**válassza **hálózati közreműködő**. 
   
   1. Alatt **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.

   1. A **kiválasztása**, adja meg **Azure Logic Apps**. 

   1. Miután a tagok listája megjelenik, válassza ki **Azure Logic Apps**. 

      > [!TIP]
      > Ha ez a szolgáltatás nem találja, adja meg a Logic Apps szolgáltatás Alkalmazásazonosító: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` 
   
   1. Ha elkészült, kattintson a **Mentés** gombra.

   Példa:

   ![Szerepkör-hozzárendelés hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

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
   | **Virtuális hálózat** | Igen | <*Az Azure-virtuális hálózat neve*> | Az Azure virtuális hálózat, ahol szeretné a környezet betöltése, hogy a logic apps, a környezetben hozzáférhessen a virtuális hálózat. Ha nem rendelkezik olyan hálózattal, létrehozhat egy itt. <p>**Fontos**: Is *csak* észrevegye hajtható végre, ha a hoz létre az ISE-ben. Azonban ez a kapcsolat létrehozásához, győződjön meg arról, hogy már [beállítása szerepköralapú hozzáférés-vezérlés a virtuális hálózat az Azure Logic Apps](#vnet-access). | 
   | **Alhálózatok** | Igen | <*IP-címtartomány*> | Az ISE-ben szükséges négy *üres* alhálózatokat. Ezen alhálózatok undelegated bármely szolgáltatás és -erőforrások létrehozását a környezetében használt. Ön *nem módosítható* ezen IP-címtartományok a környezet létrehozása után. <p><p>Minden egyes alhálózat létrehozásához [a táblázat alatti lépéseket követve](#create-subnet). Minden alhálózati ezeknek a feltételeknek kell megfelelnie: <p>-Olyan nevet, amely nem kezdődik számmal vagy kötőjel használ. <br>– Használja az [Classless Inter-Domain Routing (CIDR) formátum](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). <br>-A-B osztály cím terület szükséges. <br>-Tartalmaz egy `/27`. Például adja meg a minden alhálózat egy 32 bites címtartomány: `10.0.0.0/27`, `10.0.0.32/27`, `10.0.0.64/27`, és `10.0.0.96/27`. <br>-Kell megadni. |
   |||||

   <a name="create-subnet"></a>

   **Alhálózat létrehozása**

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

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Logikai alkalmazás létrehozása – ISE-ben

Az integrációs service-környezet (ISE) használó logikai alkalmazások létrehozása, kövesse a [Logic Apps-alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) , de a következő eltérésekkel: 

* Létrehozásakor a logikai alkalmazást, a a **hely** tulajdonság, válassza ki az ISE-ben a a **integrációs service-környezetek** részben, például:

  ![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Az azonos beépített eseményindítók és műveletek, például a HTTP-n a azonos ISE-ben, a logikai alkalmazás futtatásához, amelyek is használhatja. Az összekötők a **ISE** címkézését is az azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

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
