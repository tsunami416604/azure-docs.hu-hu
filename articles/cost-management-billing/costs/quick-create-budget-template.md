---
title: Gyorsútmutató – Költségvetés létrehozása Azure Resource Manager-sablonnal
description: Költségvetés Azure Resource Manager-sablonnal való létrehozását ismertető gyorsútmutató.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: subject-armqs
ms.openlocfilehash: de24895334ec4c864e6daae84a6aab47a47d7b9b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103633"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Gyorsútmutató: Költségvetés létrehozása Azure Resource Manager-sablonnal

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Segítséget nyújtanak mások tájékoztatásához a kiadásaikról a költségek proaktív módon történő kezelése, és a kiadások adott időszakban való alakulásának monitorozása érdekében. A létrehozott költségvetési küszöbértékek túllépése esetén a rendszer értesítéseket aktivál. A túllépés egyik erőforrásra sincs hatással, és a felhasználást sem állítja le a rendszer. A költségek elemzése során a költségvetésekkel összehasonlíthatja és nyomon követheti a kiadásokat. Ez a gyorsútmutató ismerteti, hogyan hozható létre költségvetés Resource Manager-sablonnal.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Resource Manager-sablon csak Azure-előfizetéseket támogat a Nagyvállalati Szerződések (EA) esetében. A sablon nem támogat más előfizetés-típusokat.

A költségvetések létrehozásához és kezeléséhez közreműködői jogosultsággal kell rendelkeznie. Létrehozhat külön költségvetéseket EA-előfizetésekhez és -erőforráscsoportokhoz. Nagyvállalati szerződéssel rendelkező számlázási fiókokhoz azonban nem hozhat létre költségvetéseket. Az Azure EA-előfizetések esetében a költségvetések megtekintéséhez olvasási jogosultsággal kell rendelkeznie.

A költségvetés létrehozása után legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához a költségvetések megtekintéséhez.

Új előfizetés esetén nem hozhat létre azonnal költségvetést, és a Cost Management további szolgáltatásait sem használhatja azonnal. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

Az alábbi Azure-engedélyek, vagy -hatókörök, támogatottak az egyes előfizetésekben a költségvetések felhasználó vagy csoport általi létrehozásához. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő és Cost Management-közreműködő – A saját költségvetéseit hozhatja létre, módosíthatja vagy törölheti. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és Cost Management-olvasó – Megtekintheti azokat a költségvetéseket, amelyekhez engedéllyel rendelkezik.

További információ a Cost Management adataihoz való hozzáférés hozzárendeléséről: [A Cost Management adataihoz való hozzáférés hozzárendelése](assign-access-acm-data.md).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://azure.microsoft.com/resources/templates/create-budget) származik.

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json":::

A sablonban egyetlen Azure-erőforrás van definiálva:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Azure-költségvetés létrehozása.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy költségvetést.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json"><img src="./media/quick-create-budget-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

   [![Resource Manager-sablon, költségvetés létrehozása, portál üzembe helyezése](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **Új létrehozása** lehetőséget, és adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson az **OK** gombra, vagy válasszon egy meglévő erőforráscsoportot.
    * **Hely**: válasszon ki egy helyet. Például: **USA középső régiója**.
    * **Költségvetés neve**: adja meg a költségvetés nevét. Egyedinek kell lennie az erőforráscsoporton belül. Csak alfanumerikus, aláhúzásjel és kötőjel karakterek engedélyezettek.
    * **Összeg**: adja meg a költségek vagy a használat költségvetéssel nyomon követendő teljes összegét.
    * **Költségvetési kategória**: válassza ki a költségvetés kategóriáját, illetve azt, hogy a költségvetéses nyomon követése **Költség** vagy **Használat** alapján történjen.
    * **Időfelbontási szint**: adja meg a költségvetés időtartamát. Az engedélyezett értékek: Havi, Negyedéves és Éves. Az időfelbontási szint végén a költségvetés visszaáll az alaphelyzetre.
    * **Kezdő dátum**: adja meg a kezdő dátumot a hónap első napjával, ÉÉÉÉ-HH-NN formátumban. A jövőbeli kezdő dátumoknak a mai dátumhoz képest három hónapon belül kell lenniük. Az időfelbontási szint időtartammal megadhat múltbeli kezdő dátumot is.
    * **Záró dátum**: adja meg a költségvetés záró dátumát ÉÉÉÉ-HH-NN formátumban. Ha nincs megadva, az alapértelmezett érték a kezdő dátumhoz képest 10 évvel későbbre van állítva.
    * **Operátor**: válasszon egy összehasonlító operátort. A lehetséges értékek: EqualTo, GreaterThan vagy GreaterThanOrEqualTo.
    * **Küszöbérték**: adja meg az értesítési küszöbértéket. A rendszer értesítést küld, ha a költség meghaladja a küszöbértéket. Ez mindig egy százalékos érték, 0 és 1000 között.
    * **Kapcsolattartási e-mail-címek**: adja meg azon e-mail-címek listáját, ahová a rendszer költségvetési értesítést küld, ha túllépi a küszöbértéket. A várt formátum: `["user1@domain.com","user2@domain.com"]`.
    * **Kapcsolattartási szerepkörök**: adja meg azon kapcsolattartási szerepkörök listáját, ahová a rendszer költségvetési értesítést küld, ha túllépi a küszöbértéket. Az alapértelmezett értékek: Tulajdonos, Közreműködő és Olvasó. A várt formátum: `["Owner","Contributor","Reader"]`.
    * **Kapcsolattartási csoportok**: adja meg azon műveletcsoportok listáját, ahová a rendszer költségvetési értesítést küld, ha túllépi a küszöbértéket. Sztringtömböt fogad el. A várt formátum: `["Action Group Name1","Action Group Name2"]`. Ha nem szeretne műveletcsoportokat használni, adja meg a következőt: `[]`.
    * **Erőforrások szűrő**: adja meg az erőforrások szűrőinek listáját. A várt formátum: `["Resource Filter Name1","Resource Filter Name2"]`. Ha nem szeretne szűrőt alkalmazni, adja meg a következőt: `[]`. Ha megad egy erőforrásszűrőt, akkor meg kell adnia a **mérőszámszűrők** értékeit is.
    * **Mérőszámok szűrő**: adja meg a mérőszámok szűrőinek listáját, ami kötelező a **Használat** költségvetési kategóriához tartozó költségvetések esetében. A várt formátum: `["Meter Filter Name1","Meter Filter Name2"]`. Ha nem adott meg **erőforrásszűrőt**, adja meg a következőt: `[]`.
    * **Elfogadom a fenti használati feltételeket**: Válassza ezt.

3. Válassza a **Beszerzés** lehetőséget. A költségvetés sikeres üzembe helyezése után megjelenik egy értesítés:

   ![Resource Manager-sablon, költségvetés, portál üzembe helyezésével kapcsolatos értesítés](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Az Azure Portalon helyezhető üzembe a sablon. Az Azure Portalon kívül használhatja a következőket is: Azure PowerShell, Azure CLI és REST API. Az egyéb üzembehelyezési sablonokról további információért lásd: [Sablonok üzembe helyezése](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az Azure Portalon ellenőrizheti, hogy létrejött-e a költségvetés. Ezt itt teheti meg: **Költségkezelés + Számlázás** > válasszon egy hatókört > **Költségvetések**. Esetleg az alábbi Azure CLI- vagy Azure PowerShell-szkriptekkel tekintheti meg a költségvetést.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="next-steps"></a>További lépések

Ebben a gyorsútmutatóban létrehozott egy Azure-költségvetést az üzemelő példányban. Az Azure-költségkezeléssel és -számlázással, illetve az Azure Resource Managerrel kapcsolatos további információkat az alábbi cikkekben talál.

- Olvassa el a [Költségkezelés és számlázás](../cost-management-billing-overview.md) áttekintését
- [Költségvetések létrehozása](tutorial-acm-create-budgets.md) az Azure Portalon
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)