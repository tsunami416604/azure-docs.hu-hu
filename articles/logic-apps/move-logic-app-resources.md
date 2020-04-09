---
title: Logikai alkalmazások áttelepítése előfizetések, erőforráscsoportok vagy régiók között
description: Logikai alkalmazások vagy integrációs fiókok áttelepítése más Azure-előfizetésekbe, erőforráscsoportokba vagy helyekre (régiókba)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878731"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Logikai alkalmazás-erőforrások áthelyezése más Azure-erőforráscsoportokba, régiókba vagy előfizetésekbe

A logikai alkalmazás vagy a kapcsolódó erőforrások áttelepítése egy másik Azure-erőforráscsoport, régió vagy előfizetés, számos módon végezheti el ezeket a feladatokat, például az Azure Portalon, az Azure PowerShell, az Azure CLI és a REST API. Az erőforrások áthelyezése előtt tekintse át az alábbi szempontokat: 

* Csak adott [logikai alkalmazás erőforrás-típusok](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) at helyezhetát át az Azure-erőforráscsoportok vagy előfizetések között.

* Az [limits](../logic-apps/logic-apps-limits-and-config.md) Azure-előfizetésben és az egyes Azure-régiókban rendelkezhet logikai alkalmazás-erőforrások számának korlátozásai. Ezek a korlátok befolyásolják, hogy áthelyezhet-e adott erőforrástípusokat, ha a régió változatlan marad az előfizetések vagy erőforráscsoportok között. Például minden Azure-előfizetésben csak egy ingyenes réteg-integrációs fiókkal rendelkezhet minden Egyes Azure-régióhoz.

* Erőforrások áthelyezésekek, az Azure új erőforrás-azonosítókat hoz létre. Ezért győződjön meg arról, hogy az új azonosítók helyett, és frissítse a parancsfájlok vagy eszközök, amelyek az áthelyezett erőforrásokat.

* Miután áttelepítette a logikai alkalmazásokat az előfizetések, erőforráscsoportok vagy régiók között, újra létre kell hoznia vagy újra engedélyeznie kell a nyílt hitelesítést (OAuth) igénylő kapcsolatokat.

* Az [integrációs szolgáltatási környezet (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) csak egy másik erőforráscsoport, amely ugyanabban az Azure-régióban vagy az Azure-előfizetésben található. Az ISE nem helyezhető át egy másik Azure-régióban vagy Azure-előfizetésben létező erőforráscsoportba. Egy ilyen áthelyezés után frissítenie kell az ISE-re mutató összes hivatkozást a logikai alkalmazás munkafolyamataiban, az integrációs fiókokban, a kapcsolatokban és így tovább.

## <a name="prerequisites"></a>Előfeltételek

* Ugyanaz az Azure-előfizetés, amelyet az áthelyezni kívánt logikai alkalmazás vagy integrációs fiók létrehozásához használt

* Az erőforrás-tulajdonos engedélyei a kívánt erőforrások áthelyezéséhez és beállításához. További információ a [szerepköralapú hozzáférés-vezérlésről (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Erőforrások áthelyezése különböző előfizetések között

To move a resource, such as a logic app or integration account, to another Azure subscription, you can use the Azure portal, Azure PowerShell, Azure CLI, or REST API. Ezek a lépések az Azure Portalra vonatkoznak, amelyet akkor használhat, ha az erőforrás régiója ugyanaz marad. További lépések és általános előkészítés: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az áthelyezni kívánt logikai alkalmazás-erőforrást.

1. Az erőforrás **Áttekintés lapján,** az **Előfizetés**csoportban válassza a **módosítási** hivatkozást.

1. Az **Erőforrások áthelyezése** lapon jelölje ki a logikai alkalmazás erőforrását és az áthelyezni kívánt kapcsolódó erőforrásokat.

1. Az **Előfizetés** ek listájában válassza ki a cél-előfizetést.

1. Az **Erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik lehetőségként másik erőforráscsoport létrehozásához válassza **az Új csoport létrehozása**lehetőséget.

1. Ha meg szeretné erősíteni, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem működnek, amíg nem frissíti őket az új erőforrásazonosítókkal, jelölje be a megerősítést kérő párbeszédpanelt, majd kattintson az **OK gombra.**

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Erőforrások áthelyezése erőforráscsoportok között

Egy erőforrás, például egy logikai alkalmazás, integrációs fiók vagy [integrációs szolgáltatási környezet (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md)áthelyezéséhez egy másik Azure-erőforráscsoportba, használhatja az Azure Portalon, az Azure PowerShell, az Azure CLI vagy a REST API.To move a resource, such as a logic app, integration account, or integration service environment (ISE) , to another Azure resource group, you can use the Azure Portal, Azure PowerShell, Azure CLI, vagy REST API. Ezek a lépések az Azure Portalra vonatkoznak, amelyet akkor használhat, ha az erőforrás régiója ugyanaz marad. További lépések és általános előkészítés: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Mielőtt ténylegesen áthelyezi az erőforrásokat a csoportok között, tesztelheti, hogy sikeresen áthelyezheti-e az erőforrást egy másik csoportba. További információt [az Áthelyezés ellenőrzése című](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)témakörben talál.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az áthelyezni kívánt logikai alkalmazás-erőforrást.

1. Az erőforrás **Áttekintés lapján,** az **Erőforrás csoport**csoport elemmellett válassza a **módosítási** hivatkozást.

1. Az **Erőforrások áthelyezése** lapon jelölje ki a logikai alkalmazás erőforrását és az áthelyezni kívánt kapcsolódó erőforrásokat.

1. Az **Erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik lehetőségként másik erőforráscsoport létrehozásához válassza **az Új csoport létrehozása**lehetőséget.

1. Ha meg szeretné erősíteni, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem működnek, amíg nem frissíti őket az új erőforrásazonosítókkal, jelölje be a megerősítést kérő párbeszédpanelt, majd kattintson az **OK gombra.**

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Erőforrások áthelyezése régiók között

Ha egy logikai alkalmazást egy másik régióba szeretne áthelyezni, a beállítások a logikai alkalmazás létrehozásának módjától függenek. A választott beállítás alapján újra létre kell hoznia, vagy újra engedélyeznie kell a kapcsolatokat a logikai alkalmazásban.

* Az Azure Portalon hozza létre újra a logikai alkalmazást az új régióban, és konfigurálja újra a munkafolyamat-beállításokat. Az időmegtakarítás érdekében átmásolhatja az alapul szolgáló munkafolyamat-definíciót és kapcsolatokat a forrásalkalmazásból a célalkalmazásba. A logikai alkalmazás mögötti "kód" megtekintéséhez a Logic App Designer eszköztáron válassza a **Kód nézet lehetőséget.**

* A Visual Studio és az Azure Logic Apps Tools for Visual Studio használatával azure [Resource Manager-sablonként](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) [megnyithatja és letöltheti a logikai alkalmazást](../logic-apps/manage-logic-apps-with-visual-studio.md) az Azure Portalról. Ez a sablon többnyire készen áll a központi telepítésre, és tartalmazza a logikai alkalmazás erőforrás-definícióit, beleértve magát a munkafolyamatot és a kapcsolatokat. A sablon a központi telepítéssorán használandó értékek paramétereit is deklarálja. Így könnyebben módosíthatja, hogy hol és hogyan telepítse a logikai alkalmazást az igényeinek megfelelően. A telepítés helyének és egyéb szükséges információinak megadásához használjon külön paraméterfájlt.

* Ha a logikai alkalmazást folyamatos integrációs (CI) és folyamatos kézbesítési (CD) eszközökkel hozta létre és telepítette, például az Azure-folyamatok az Azure DevOps-ban, telepítheti az alkalmazást egy másik régióba ezekkel az eszközökkel.

A logikai alkalmazások központi telepítési sablonjairól az alábbi témakörökben olvashat bővebben:

* [Áttekintés: Az Azure Logic Apps üzembe helyezésének automatizálása az Azure Resource Manager-sablonok használatával](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [A logikai alkalmazás megkeresése, megnyitása és letöltése az Azure Portalról a Visual Studióba](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Resource Manager-sablonok létrehozása az Azure Logic Apps alkalmazásokhoz](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sablonok üzembe helyezése az Azure Logic Apps alkalmazásokhoz](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

Egyes Azure-erőforrások, például a helyszíni adatátjáró-erőforrások az Azure-ban, létezhet egy olyan régióban, amely eltér az erőforrásokat használó logikai alkalmazások. Azonban más Azure-erőforrások, például a csatolt integrációs fiókok, léteznie kell ugyanabban a régióban, mint a logikai alkalmazások. A forgatókönyv alapján győződjön meg arról, hogy a logikai alkalmazások hozzáférhetnek az erőforrásokhoz, amelyek az alkalmazások várhatóan ugyanabban a régióban.

Ha például egy logikai alkalmazást egy integrációs fiókhoz szeretne kapcsolni, mindkét erőforrásnak ugyanabban a régióban kell lennie. Olyan esetekben, mint a vész-helyreállítási, általában szeretné integrációs fiókok, amelyek azonos konfigurációval és összetevők. Más esetekben előfordulhat, hogy különböző konfigurációkkal és összetevőkkel rendelkező integrációs fiókokra van szükség.

Az Azure Logic Apps egyéni összekötők láthatók az összekötők szerzői és felhasználói számára, akik ugyanazt az Azure-előfizetést és ugyanazt az Azure Active Directory-bérlőt rendelkeznek. Ezek az összekötők ugyanabban a régióban érhetők el, ahol a logikai alkalmazások vannak telepítve. További információért lásd az [egyéni összekötők a vállalaton belüli megosztását](https://docs.microsoft.com/connectors/custom-connectors/share) ismertető cikket.

A Visual Studio által kapott sablon csak a logikai alkalmazás és a kapcsolatok erőforrás-definícióit tartalmazza. Így ha a logikai alkalmazás más erőforrásokat használ, például egy integrációs fiókot és a B2B-összetevőket, például a partnereket, a megállapodásokat és a sémákat, az Azure Portalhasználatával exportálnia kell az integrációs fiók sablonját. Ez a sablon tartalmazza az integrációs fiók és az összetevők erőforrás-definícióit. A sablon azonban nincs teljesen paraméterezve. Ezért manuálisan kell paramétereznie a központi telepítéshez használni kívánt értékeket.

### <a name="export-templates-for-integration-accounts"></a>Integrációs fiókok sablonjainak exportálása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot.

1. Az integrációs fiók menüjében, a **Beállítások**csoportban válassza a **Sablon exportálása lehetőséget.**

1. Az eszköztáron válassza a **Letöltés**lehetőséget, és mentse a sablont.

1. Nyissa meg és szerkesztse a sablont a telepítéshez szükséges értékek paraméterezéséhez.

## <a name="next-steps"></a>További lépések

[Az Azure-erőforrások áthelyezése új erőforráscsoportokba vagy -előfizetésekbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
