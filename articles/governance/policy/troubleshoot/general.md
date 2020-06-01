---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a szabályzat-definíciókat, a különböző SDK-t és a Kubernetes bővítményét.
ms.date: 05/22/2020
ms.topic: troubleshooting
ms.openlocfilehash: b1cb8ab51ecc00610f1e04532ba3063be5415607
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234173"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Hibák elhárítása a Azure Policy használatával

A házirend-definíciók létrehozásakor, az SDK-val való együttműködésben vagy a Kubernetes-bővítmény [Azure Policy](../concepts/policy-for-kubernetes.md) beállításában hibák léphetnek fel. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

A hiba részleteinek helye a hibát okozó művelettől függ.

- Ha egyéni házirenddel dolgozik, próbálja ki a Azure Portal a séma vagy az eredményül kapott [megfelelőségi](../how-to/get-compliance-data.md) információk átadásával kapcsolatban, hogy láthassa az erőforrások kiértékelésének módját.
- Ha különböző SDK-val dolgozik, az SDK részletesen ismerteti, hogy a függvény miért nem sikerült.
- A Kubernetes beépülő moduljának használatakor Kezdje a [naplózást](../concepts/policy-for-kubernetes.md#logging) a fürtben.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-alias-not-found"></a>Forgatókönyv: az alias nem található

#### <a name="issue"></a>Probléma

A Azure Policy az [aliasokat](../concepts/definition-structure.md#aliases) használja az erőforrás-kezelő tulajdonságaihoz való leképezéshez.

#### <a name="cause"></a>Ok

Egy házirend-definícióban helytelen vagy nem létező alias van használatban.

#### <a name="resolution"></a>Megoldás:

Először ellenőrizze, hogy a Resource Manager-tulajdonságnak van-e aliasa. Az elérhető aliasok megkereséséhez használja [Azure Policy bővítményt a Visual Studio Code](../how-to/extension-for-vscode.md), az [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)vagy az SDK használatával. Ha egy Resource Manager-tulajdonság aliasa nem létezik, hozzon létre egy támogatási jegyet.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Forgatókönyv: az értékelés részletei nem naprakészek

#### <a name="issue"></a>Probléma

Egy erőforrás a "nincs elindítva" állapotban van, vagy a megfelelőségi adatok nem aktuálisak.

#### <a name="cause"></a>Ok

Az új szabályzatok vagy kezdeményezési hozzárendelések alkalmazása körülbelül 30 percet vesz igénybe. Egy meglévő hozzárendelés hatókörén belüli új vagy frissített erőforrások körülbelül 15 perccel később elérhetővé válnak. A standard megfelelőségi vizsgálat 24 óránként történik. További információ: [kiértékelési eseményindítók](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Megoldás:

Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.

### <a name="scenario-evaluation-not-as-expected"></a>Forgatókönyv: a kiértékelés nem a várt módon történik

#### <a name="issue"></a>Probléma

Egy erőforrás nem a _megfelelő_ vagy _nem megfelelő_, az adott erőforrás számára várt értékelési állapotban van.

#### <a name="cause"></a>Ok

Az erőforrás nem a megfelelő hatókörben van a házirend-hozzárendeléshez, vagy a házirend-definíció nem a kívánt módon működik.

#### <a name="resolution"></a>Megoldás:

- Egy nem megfelelő erőforráshoz, amelynek a megfelelőségét várták, a meg [nem felelés okainak meghatározásával](../how-to/determine-non-compliance.md)kezdheti meg. A definíció és a kiértékelt tulajdonság értékének összehasonlítása azt jelzi, hogy az erőforrás miért nem megfelelő.
- A nem megfelelőnek minősülő megfelelő erőforrás esetén olvassa el a házirend-definíció feltételét, és értékelje ki az erőforrások tulajdonságait. Ellenőrizze, hogy a logikai operátorok a megfelelő feltételeket csoportosítják-e, és hogy a feltételek ne legyenek invertálva.

Ha a szabályzat-hozzárendelés megfelelősége `0/0` erőforrásokat mutat, a hozzárendelési hatókörön belül nem határoztak meg erőforrásokat. A házirend-definíciót és a hozzárendelési hatókört egyaránt ellenőriznie kell.

### <a name="scenario-enforcement-not-as-expected"></a>Forgatókönyv: a végrehajtás nem a várt módon történik

#### <a name="issue"></a>Probléma

Az Azure Policy által várhatóan végrehajtott erőforrás nem, és nincs bejegyzés az Azure-beli [tevékenység naplójában](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Ok

A házirend-hozzárendelés _le_lett állítva a [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) . Míg a kényszerítési mód le van tiltva, a házirend hatálya nem kényszerített, és nincs bejegyzés a tevékenység naplójában.

#### <a name="resolution"></a>Megoldás:

Frissítse a **enforcementMode** az _engedélyezett_értékre. Ez a módosítás lehetővé teszi, hogy Azure Policy a szabályzat-hozzárendelés erőforrásaira, és bejegyzéseket küldjön a tevékenységi naplóba. Ha a **enforcementMode** már engedélyezve van, tekintse meg a következő témakört: [kiértékelés nem várt módon](#scenario-evaluation-not-as-expected) a tanfolyamok.

### <a name="scenario-denied-by-azure-policy"></a>Forgatókönyv: Azure Policy megtagadva

#### <a name="issue"></a>Probléma

Egy erőforrás létrehozása vagy frissítése megtagadva.

#### <a name="cause"></a>Ok

A hatókörhöz tartozó szabályzat-hozzárendelés az új vagy frissített erőforrás megfelel egy [megtagadási](../concepts/effects.md#deny) hatású házirend-definíció feltételeinek. Ezek a definíciók nem hozhatók létre vagy nem frissíthetők.

#### <a name="resolution"></a>Megoldás:

Egy megtagadási szabályzat-hozzárendelés hibaüzenete tartalmazza a szabályzat-definíciót és a szabályzat-hozzárendelési azonosítókat. Ha az üzenetben szereplő hibaüzenetek nem maradnak meg, akkor a [tevékenység naplójában](../../../azure-monitor/platform/activity-log-view.md)is elérhető. Ezekkel az információkkal további részleteket tudhat meg az erőforrás-korlátozásokról, és módosíthatja a kérésben szereplő erőforrás-tulajdonságokat az engedélyezett értékek egyeztetéséhez.

## <a name="template-errors"></a>Sablon hibái

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Forgatókönyv: a szabályzat által támogatott függvények sablon szerint dolgozva

#### <a name="issue"></a>Probléma

Azure Policy számos Resource Manager-sablon függvényt és függvényt támogat, amelyek csak egy házirend-definícióban érhetők el. A Resource Manager ezeket a függvényeket egy központi telepítés részeként dolgozza fel a házirend-definíció részeként.

#### <a name="cause"></a>Ok

A támogatott függvények, például a vagy a használata esetén a `parameter()` `resourceGroup()` függvény feldolgozott eredménye a központi telepítéskor, a házirend-definícióhoz való kilépés és a Azure Policy motor feldolgozása helyett.

#### <a name="resolution"></a>Megoldás:

Ha egy függvényt át szeretne adni egy házirend-definíció részévé, a teljes karakterláncot a `[` tulajdonsághoz hasonló módon kell kinéznie `[[resourceGroup().tags.myTag]` . A escape-karakter hatására a Resource Manager az értéket karakterláncként kezeli a sablon feldolgozásakor. Azure Policy ezután a függvényt a házirend-definícióba helyezi, ami lehetővé teszi, hogy az a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Telepítési hibák a bővítményben

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Forgatókönyv: a Helm diagram használatával történő telepítés sikertelen a jelszóval

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs a következő üzenetek egyikével meghiúsul:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Ok

A generált jelszó tartalmaz egy vesszőt ( `,` ), amely a Helm diagramra van felosztva.

#### <a name="resolution"></a>Megoldás:

A jelszó értékében válassza a vessző ( `,` ) értéket, ha `helm install azure-policy-addon` fordított perjelet ( `\` ) használ.

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Forgatókönyv: a telepítés a Helm diagram használatával meghiúsul, mert a név már létezik

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs a következő üzenettel meghiúsul:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Ok

A (z) nevű Helm `azure-policy-addon` -diagram már telepítve van vagy részben telepítve van.

#### <a name="resolution"></a>Megoldás:

Kövesse az utasításokat a [Kubernetes-bővítmény Azure Policy eltávolításához](../concepts/policy-for-kubernetes.md#remove-the-add-on), majd futtassa újra a `helm install azure-policy-addon` parancsot.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat a szakértőktől a [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-policy.html)használatával.
- Az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal csatlakozhat a felhasználói élmény fokozásához: válaszok, támogatás és szakértők.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
