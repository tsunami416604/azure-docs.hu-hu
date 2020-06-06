---
title: Üzembehelyezési előzmények törlései
description: Ismerteti, hogyan Azure Resource Manager automatikusan törli a központi telepítéseket az üzembe helyezési előzményekből. A központi telepítések akkor törlődnek, ha az előzmények meghaladják a 800-as korlátot.
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2d87cc1dcc0d3a41cb32bf7750ccdd832019f8bf
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462736"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatikus törlés az üzembe helyezési előzményekből

Minden alkalommal, amikor telepít egy sablont, a központi telepítésre vonatkozó információkat az üzembe helyezési előzményekbe írja a rendszer. Az egyes erőforráscsoportok az üzembe helyezési előzményekben legfeljebb 800 üzemelő példányra korlátozódnak.

A június 2020-től kezdődően Azure Resource Manager automatikusan törli az előzményekből származó üzembe helyezéseket a korlát közelében. Az automatikus törlés a korábbi viselkedés változása. Korábban a telepítési előzményekből manuálisan kellett törölni a központi telepítéseket, hogy elkerülje a hiba beolvasását.

> [!NOTE]
> Egy központi telepítés az előzményekből való törlése nem befolyásolja az üzembe helyezett erőforrásokat.

## <a name="when-deployments-are-deleted"></a>Központi telepítések törlésekor

A központi telepítések csak akkor törlődnek az üzembe helyezési előzményekből, ha az 800-es korlát közelében van. Azure Resource Manager törli a legrégebbi üzemelő példányok egy kis készletét, így a későbbi központi telepítések számára nem szabad helyet kialakítani. Az előzmények többsége változatlan marad. A legrégebbi üzemelő példányokat a rendszer mindig először törli.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Törlések az üzembe helyezési előzményekből":::

Az üzemelő példányokon kívül a törléseket is aktiválhatja a [művelet](template-deploy-what-if.md) végrehajtásakor vagy a központi telepítés ellenőrzésekor.

Ha a központi telepítéshez ugyanazt a nevet adja, mint az előzményekben, akkor alaphelyzetbe állítja a helyet az előzményekben. Az üzembe helyezés az előzmények legutóbbi helyére kerül. Alaphelyzetbe állít egy központi telepítést is, ha egy hiba után visszaállítja az [adott központi telepítésre](rollback-on-error.md) .

> [!NOTE]
> Ha az erőforráscsoport már a 800-es korláttal rendelkezik, a következő telepítés hibát jelez. Az automatikus törlési folyamat azonnal elindul. A központi telepítést rövid várakozás után is kipróbálhatja.

## <a name="opt-out-of-automatic-deletions"></a>Automatikus törlés engedélyezése

Az előzményekből letilthatja az automatikus törlést. **Ezt a lehetőséget csak akkor használja, ha saját maga szeretné kezelni az üzembe helyezési előzményeket.** Az előzményekben az 800-es üzemelő példányok korlátja továbbra is érvényben van. Ha túllépi az 800-es üzemelő példányokat, hibaüzenetet kap, és a telepítés sikertelen lesz.

Az automatikus törlés letiltásához regisztrálja a `Microsoft.Resources/DisableDeploymentGrooming` szolgáltatás jelölőjét. Ha regisztrálja a szolgáltatás jelzőjét, a teljes Azure-előfizetés automatikus törlését is elvégzi. Csak egy adott erőforráscsoport esetében lehet letiltani.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell esetében használja a [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Az előfizetés aktuális állapotának megtekintéséhez használja a következőt:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetében használja az [az Feature Register lehetőséget](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Az előfizetés aktuális állapotának megtekintéséhez használja a következőt:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

REST API használja a [szolgáltatások – regisztráció lehetőséget](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Az előfizetés aktuális állapotának megtekintéséhez használja a következőt:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Következő lépések

* Az üzembe helyezési előzmények megtekintésével kapcsolatos további tudnivalókért lásd: az [üzembe helyezési előzmények megtekintése Azure Resource Manager](deployment-history.md)használatával.
