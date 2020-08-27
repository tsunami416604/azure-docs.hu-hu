---
title: Gyorsútmutató – Költségvetés létrehozása Azure Resource Manager-sablonnal
description: Költségvetés Azure Resource Manager-sablonnal való létrehozását ismertető gyorsútmutató.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs
ms.openlocfilehash: 3b21353c7e5338c78b9d56e79ac5da3fa7e9af84
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687586"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Gyorsútmutató: Költségvetés létrehozása ARM-sablonnal

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Segítséget nyújtanak mások tájékoztatásához a kiadásaikról a költségek proaktív módon történő kezelése, és a kiadások adott időszakban való alakulásának monitorozása érdekében. A létrehozott költségvetési küszöbértékek túllépése esetén a rendszer értesítéseket aktivál. A túllépés egyik erőforrásra sincs hatással, és a felhasználást sem állítja le a rendszer. A költségek elemzése során a költségvetésekkel összehasonlíthatja és nyomon követheti a kiadásokat. Ez a gyorsútmutató ismerteti, hogyan hozható létre költségvetés Azure Resource Manager-sablonnal (ARM-sablonnal).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Új előfizetés esetén nem hozhat létre azonnal költségvetést, és a Cost Management további szolgáltatásait sem használhatja azonnal. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

A költségvetések az Azure-fiókok és -hatókörök következő típusai esetén támogatottak:

- Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hatókörök
    - Felügyeleti csoportok
    - Előfizetés
- A Nagyvállalati Szerződés hatókörei
    - Számlázási fiók
    - Részleg
    - Regisztrációs fiók
- Egyedi szerződések
    - Számlázási fiók
- A Microsoft-ügyfélszerződés hatókörei
    - Számlázási fiók
    - Számlázási profil
    - Számlázási szakasz
    - Ügyfél
- AWS-hatókörök
    - Külső fiók
    - Külső előfizetés

A költségvetés megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

Az Azure EA-előfizetések esetében a költségvetések megtekintéséhez olvasási jogosultsággal kell rendelkeznie. A költségvetések létrehozásához és kezeléséhez közreműködői jogosultsággal kell rendelkeznie.

Az alábbi Azure-engedélyek, vagy -hatókörök, támogatottak az egyes előfizetésekben a költségvetések felhasználó vagy csoport általi létrehozásához. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő és Cost Management-közreműködő – A saját költségvetéseit hozhatja létre, módosíthatja vagy törölheti. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és Cost Management-olvasó – Megtekintheti azokat a költségvetéseket, amelyekhez engedéllyel rendelkezik.

További információ a Cost Management adataihoz való hozzáférés hozzárendeléséről: [A Cost Management adataihoz való hozzáférés hozzárendelése](assign-access-acm-data.md).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/create-budget) közül származik.

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

A sablonban egyetlen Azure-erőforrás van definiálva:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Azure-költségvetés létrehozása.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy költségvetést.

   [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Resource Manager-sablon, költségvetés létrehozása, portál üzembe helyezése]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: ha szükséges, jelöljön ki egy meglévő erőforráscsoportot, vagy **hozzon létre egy újat**.
    * **Régió**: válasszon ki egy Azure-régiót. Például: **USA középső régiója**.
    * **Költségvetés neve**: adja meg a költségvetés nevét. Egyedinek kell lennie az erőforráscsoporton belül. Csak alfanumerikus, aláhúzásjel és kötőjel karakterek engedélyezettek.
    * **Összeg**: adja meg a költségek költségvetéssel nyomon követendő teljes összegét.
    * **Időfelbontási szint**: adja meg a költségvetés időtartamát. Az engedélyezett értékek: Havi, Negyedéves és Éves. Az időfelbontási szint végén a költségvetés visszaáll az alaphelyzetre.
    * **Kezdő dátum**: adja meg a kezdő dátumot a hónap első napjával, ÉÉÉÉ-HH-NN formátumban. A jövőbeli kezdő dátumoknak a mai dátumhoz képest három hónapon belül kell lenniük. Az időfelbontási szint időtartammal megadhat múltbeli kezdő dátumot is.
    * **Záró dátum**: adja meg a költségvetés záró dátumát ÉÉÉÉ-HH-NN formátumban. 
    * **Első küszöbérték**: adja meg az első értesítés küszöbértékét. A rendszer értesítést küld, ha a költség meghaladja a küszöbértéket. Ez mindig egy százalékos érték, 0 és 1000 között.
    * **Második küszöbérték**: adja meg a második értesítés küszöbértékét. A rendszer értesítést küld, ha a költség meghaladja a küszöbértéket. Ez mindig egy százalékos érték, 0 és 1000 között.
    * **Kapcsolattartási szerepkörök**: adja meg azon kapcsolattartási szerepkörök listáját, ahová a rendszer költségvetési értesítést küld, ha túllépi a küszöbértéket. Az alapértelmezett értékek: Tulajdonos, Közreműködő és Olvasó. A várt formátum: `["Owner","Contributor","Reader"]`.
    * **Kapcsolattartási e-mail-címek**: adja meg azon e-mail-címek listáját, ahová a rendszer költségvetési értesítést küld, ha túllépi a küszöbértéket. A várt formátum: `["user1@domain.com","user2@domain.com"]`.
    * **Kapcsolattartási csoportok**: adja meg azon műveletcsoportok erőforrás-azonosítójának listáját (teljes erőforrás-URI-ként), amelyeknek a rendszer költségvetési értesítést küld, ha túllépi a küszöbértéket. Sztringtömböt fogad el. A várt formátum: `["action group resource ID1","action group resource ID2"]`. Ha nem szeretne műveletcsoportokat használni, adja meg a következőt: `[]`.
    * **Erőforráscsoport szűrőértékei**: adja meg a szűrendő erőforráscsoport-nevek listáját. A várt formátum: `["Resource Group Name1","Resource Group Name2"]`. Ha nem szeretne szűrőt alkalmazni, adja meg a következőt: `[]`. 
    * **Mérési kategória szűrőértékei**: adja meg az Azure-szolgáltatás mérési kategóriáinak listáját. A várt formátum: `["Meter Category1","Meter Category2"]`. Ha nem szeretne szűrőt alkalmazni, adja meg a következőt: `[]`.
   
3. Az Azure-előfizetés típusától függően hajtsa végre az alábbi műveletek egyikét:
   - Válassza az **Áttekintés + létrehozás** lehetőséget.
   - Tekintse át a használati feltételeket, és válassza az **Elfogadom a fenti feltételeket és kikötéseket**, majd a **Vásárlás** lehetőséget.

4. Ha az **Felülvizsgálat + létrehozás** lehetőséget választotta, a sablon érvényesítve lesz. Kattintson a **Létrehozás** gombra.  

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a költségvetésre, a következő módszerekkel törölheti:

### <a name="azure-portal"></a>Azure Portal

Lépjen a **Költségkezelés + számlázás** területre > válasszon egy számlázási hatókört > **Költségvetések** > válasszon egy költségvetést > válassza a **Költségkeret törlése** lehetőséget.

### <a name="command-line"></a>Parancssor

A költségvetést eltávolíthatja az Azure CLI vagy az Azure PowerShell használatával.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a gyorsútmutatóban létrehozott egy Azure-költségvetést az üzemelő példányban. Az Azure-költségkezeléssel és -számlázással, illetve az Azure Resource Managerrel kapcsolatos további információkat az alábbi cikkekben talál.

- Olvassa el a [Költségkezelés és számlázás](../cost-management-billing-overview.md) áttekintését
- [Költségvetések létrehozása](tutorial-acm-create-budgets.md) az Azure Portalon
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
