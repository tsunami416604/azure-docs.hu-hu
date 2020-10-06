---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a szabályzat-definíciókat, a különböző SDK-t és a Kubernetes bővítményét.
ms.date: 10/05/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6026dc75187c8a70203a2484380eed70d519599d
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743437"
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

A Azure Policy [aliasokat](../concepts/definition-structure.md#aliases) használ a Azure Resource Manager tulajdonságok leképezéséhez.

#### <a name="cause"></a>Ok

Egy házirend-definícióban helytelen vagy nem létező alias van használatban.

#### <a name="resolution"></a>Feloldás

Először ellenőrizze, hogy a Resource Manager-tulajdonságnak van-e aliasa. Az elérhető aliasok megkereséséhez használja [Azure Policy bővítményt a Visual Studio Code](../how-to/extension-for-vscode.md), az [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)vagy az SDK használatával. Ha egy Resource Manager-tulajdonság aliasa nem létezik, hozzon létre egy támogatási jegyet.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Forgatókönyv: az értékelés részletei nem naprakészek

#### <a name="issue"></a>Probléma

Egy erőforrás a "nincs elindítva" állapotban van, vagy a megfelelőségi adatok nem aktuálisak.

#### <a name="cause"></a>Ok

Az új szabályzatok vagy kezdeményezési hozzárendelések alkalmazása körülbelül 30 percet vesz igénybe. Egy meglévő hozzárendelés hatókörén belüli új vagy frissített erőforrások körülbelül 15 perccel később elérhetővé válnak. A standard megfelelőségi vizsgálat 24 óránként történik. További információ: [kiértékelési eseményindítók](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Feloldás

Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.

### <a name="scenario-compliance-not-as-expected"></a>Forgatókönyv: nem a várt megfelelőség

#### <a name="issue"></a>Probléma

Egy erőforrás nem a _megfelelő_ vagy _nem megfelelő_, az adott erőforrás számára várt értékelési állapotban van.

#### <a name="cause"></a>Ok

Az erőforrás nem a megfelelő hatókörben van a házirend-hozzárendeléshez, vagy a házirend-definíció nem a kívánt módon működik.

#### <a name="resolution"></a>Feloldás

A házirend-definíció hibaelhárításához kövesse az alábbi lépéseket:

1. Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör helyesen van beállítva.
1. A [házirend-definíciós mód](../concepts/definition-structure.md#mode)ellenõrzése:
   - Az összes erőforrástípus "all" üzemmódja.
   - Az "indexelt" mód, ha a házirend-definíció címkéket vagy helyet keres.
1. Győződjön meg arról, hogy az erőforrás hatóköre nincs [kizárva](../concepts/assignment-structure.md#excluded-scopes) vagy [mentesített](../concepts/exemption-structure.md).
1. Ha a szabályzat-hozzárendelés megfelelősége `0/0` erőforrásokat mutat, a hozzárendelési hatókörön belül nem határoztak meg erőforrásokat. A házirend-definíciót és a hozzárendelési hatókört egyaránt ellenőriznie kell.
1. Ahhoz, hogy egy nem megfelelő erőforrást a megfelelőnek kellene lennie, vizsgálja meg a meg [nem felelés okainak megállapítását](../how-to/determine-non-compliance.md). A definíció és a kiértékelt tulajdonság értékének összehasonlítása azt jelzi, hogy az erőforrás miért nem megfelelő.
   - Ha a **célként megadott érték** helytelen, módosítsa a házirend-definíciót.
   - Ha az **aktuális érték** helytelen, ellenőrizze az erőforrás adattartalmát a használatával `resources.azure.com` .
1. Tekintse meg a hibaelhárítást: a többi gyakori probléma és megoldás esetében [nem a várt módon érvényesíthető](#scenario-enforcement-not-as-expected) .

Ha továbbra is problémája van a duplikált és testreszabott beépített szabályzat-definícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a **szabályzat készítése** területen a probléma helyes átirányításához.

### <a name="scenario-enforcement-not-as-expected"></a>Forgatókönyv: a végrehajtás nem a várt módon történik

#### <a name="issue"></a>Probléma

Az Azure Policy által várhatóan végrehajtott erőforrás nem, és nincs bejegyzés az Azure-beli [tevékenység naplójában](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Ok

A házirend-hozzárendelés _le_lett állítva a [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) . Míg a kényszerítési mód le van tiltva, a házirend hatálya nem kényszerített, és nincs bejegyzés a tevékenység naplójában.

#### <a name="resolution"></a>Feloldás

A szabályzat-hozzárendelés kényszerítésének hibaelhárításához kövesse az alábbi lépéseket:

1. Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör helyesen van beállítva, és hogy a **EnforcementMode** _engedélyezve_van. 
1. A [házirend-definíciós mód](../concepts/definition-structure.md#mode)ellenõrzése:
   - Az összes erőforrástípus "all" üzemmódja.
   - Az "indexelt" mód, ha a házirend-definíció címkéket vagy helyet keres.
1. Győződjön meg arról, hogy az erőforrás hatóköre nincs [kizárva](../concepts/assignment-structure.md#excluded-scopes) vagy [mentesített](../concepts/exemption-structure.md).
1. Ellenőrizze, hogy az erőforrás-tartalom megfelel-e a szabályzat logikájának. Ezt a [har-nyomkövetés rögzítésével](../../../azure-portal/capture-browser-trace.md) vagy az ARM-sablon tulajdonságainak áttekintésével végezheti el.
1. Hibaelhárítás ellenőrzése: az egyéb gyakori problémák és megoldások esetében [nem a várt megfelelőség](#scenario-compliance-not-as-expected) .

Ha továbbra is problémája van a duplikált és testreszabott beépített szabályzat-definícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a **szabályzat készítése** területen a probléma helyes átirányításához.

### <a name="scenario-denied-by-azure-policy"></a>Forgatókönyv: Azure Policy megtagadva

#### <a name="issue"></a>Probléma

Egy erőforrás létrehozása vagy frissítése megtagadva.

#### <a name="cause"></a>Ok

A hatókörhöz tartozó szabályzat-hozzárendelés az új vagy frissített erőforrás megfelel egy [megtagadási](../concepts/effects.md#deny) hatású házirend-definíció feltételeinek. Ezek a definíciók nem hozhatók létre vagy nem frissíthetők.

#### <a name="resolution"></a>Feloldás

Egy megtagadási szabályzat-hozzárendelés hibaüzenete tartalmazza a szabályzat-definíciót és a szabályzat-hozzárendelési azonosítókat. Ha az üzenetben szereplő hibaüzenetek nem maradnak meg, akkor a [tevékenység naplójában](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)is elérhető. Ezekkel az információkkal további részleteket tudhat meg az erőforrás-korlátozásokról, és módosíthatja a kérésben szereplő erőforrás-tulajdonságokat az engedélyezett értékek egyeztetéséhez.

## <a name="template-errors"></a>Sablon hibái

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Forgatókönyv: a szabályzat által támogatott függvények sablon szerint dolgozva

#### <a name="issue"></a>Probléma

Azure Policy számos Azure Resource Manager sablon (ARM-sablon) függvényt és függvényt támogat, amelyek csak egy házirend-definícióban érhetők el. A Resource Manager ezeket a függvényeket egy központi telepítés részeként dolgozza fel a házirend-definíció részeként.

#### <a name="cause"></a>Ok

A támogatott függvények, például a vagy a használata esetén a `parameter()` `resourceGroup()` függvény feldolgozott eredménye a központi telepítéskor, a házirend-definícióhoz való kilépés és a Azure Policy motor feldolgozása helyett.

#### <a name="resolution"></a>Feloldás

Ha egy függvényt át szeretne adni egy házirend-definíció részévé, a teljes karakterláncot a `[` tulajdonsághoz hasonló módon kell kinéznie `[[resourceGroup().tags.myTag]` . A escape-karakter hatására a Resource Manager az értéket karakterláncként kezeli a sablon feldolgozásakor. Azure Policy ezután a függvényt a házirend-definícióba helyezi, ami lehetővé teszi, hogy az a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Telepítési hibák a bővítményben

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Forgatókönyv: a Helm diagram használatával történő telepítés sikertelen a jelszóval

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs a következő üzenetek egyikével meghiúsul:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Ok

A generált jelszó tartalmaz egy vesszőt ( `,` ), amely a Helm diagramra van felosztva.

#### <a name="resolution"></a>Feloldás

A jelszó értékében válassza a vessző ( `,` ) értéket, ha `helm install azure-policy-addon` fordított perjelet ( `\` ) használ.

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Forgatókönyv: a telepítés a Helm diagram használatával meghiúsul, mert a név már létezik

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs a következő üzenettel meghiúsul:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Ok

A (z) nevű Helm `azure-policy-addon` -diagram már telepítve van vagy részben telepítve van.

#### <a name="resolution"></a>Feloldás

Kövesse az utasításokat a [Kubernetes-bővítmény Azure Policy eltávolításához](../concepts/policy-for-kubernetes.md#remove-the-add-on), majd futtassa újra a `helm install azure-policy-addon` parancsot.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat a szakértőktől a [Microsoft Q&A](/answers/topics/azure-policy.html)használatával.
- Az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal csatlakozhat a felhasználói élmény fokozásához: válaszok, támogatás és szakértők.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
