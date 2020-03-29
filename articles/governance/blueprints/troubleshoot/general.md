---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogyan háríthatja el a tervrajzok, például az irányelvek megsértésének és a tervezet paraméterfunkcióinak létrehozásával, hozzárendelésével és eltávolításával kapcsolatos problémákelhárítását.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157083"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Hibák elhárítása az Azure Blueprints használatával

A tervrajzok létrehozásakor, hozzárendelésekor vagy eltávolításakor hibák léphetnek fel. Ez a cikk ismerteti a különböző hibákat, amelyek előfordulhatnak, és hogyan lehet megoldani őket.

## <a name="finding-error-details"></a>Hibarészletek keresése

Sok hiba lesz az eredménye, hogy egy tervrajzot egy hatókörhöz rendel. Ha egy hozzárendelés sikertelen, a tervrajz részletesen ismerteti a sikertelen üzembe helyezést. Ez az információ jelzi a problémát, hogy kijavítható legyen, és a következő központi telepítés sikeres legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza **a Hozzárendelt tervrajzok** lehetőséget a bal oldali lapon, és a keresőmező segítségével szűrje a tervrajzok hozzárendeléseit a sikertelen hozzárendelés megkereséséhez. A hozzárendelések táblázatát a **Kiépítési állapot** oszlop szerint is rendezheti, hogy az összes sikertelen hozzárendelés csoportosítva jelenjen meg.

1. Kattintson a bal gombbal a _sikertelen_ állapotú tervrajzra, vagy kattintson a jobb gombbal, és válassza **a Hozzárendelés részleteinek megtekintése parancsot.**

1. A hozzárendelés sikertelenségére vonatkozó piros szalagcím a tervhozzárendelési oldal tetején található. Kattintson bárhol a banner, hogy további részleteket.

Gyakori, hogy a hibát egy ereklye okozza, nem pedig a tervrajz egésze. Ha egy összetevő létrehoz egy Key Vault és az Azure-szabályzat megakadályozza a Key Vault létrehozását, a teljes hozzárendelés sikertelen lesz.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Eset: Irányelvsértés

#### <a name="issue"></a>Probléma

A sablon központi telepítése házirend-sértés miatt nem sikerült.

#### <a name="cause"></a>Ok

Egy házirend több okból is ütközhet a központi telepítéssel:

- A létrehozás alatt létreszódó erőforrást házirend (általában termékváltozat- vagy helykorlátozások) korlátozzák.
- A központi telepítés házirend által konfigurált mezőket állít be (a címkékkel közösen)

#### <a name="resolution"></a>Megoldás:

Módosítsa a terv, így nem ütközik a szabályzatok a hiba részleteit. Ha ez a módosítás nem lehetséges, egy másik lehetőség, hogy a házirend-hozzárendelés hatóköre megváltozott, így a tervezet már nem ütközik a házirend.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Eset: Blueprint paraméter egy függvény

#### <a name="issue"></a>Probléma

A függvények tervezetparamétereit a rendszer feldolgozza, mielőtt átadnák az összetevőknek.

#### <a name="cause"></a>Ok

Egy függvényt használó tervezetparaméter átadása, például `[resourceGroup().tags.myTag]`a összetevő, azt eredményezi, hogy a függvény feldolgozása a műterméken van beállítva a dinamikus függvény helyett.

#### <a name="resolution"></a>Megoldás:

Ahhoz, hogy egy függvényt paraméterként `[` haladjon át, a `[[resourceGroup().tags.myTag]`teljes karakterláncot úgy kell kikerülnie, hogy a tervezet paramétere így néz ki. Az escape karakter hatására tervrajzok kezeli az értéket, mint egy karakterlánc feldolgozása kor a tervrajz. Tervrajzok majd elhelyezi a függvényt a műtermék, amely lehetővé teszi, hogy dinamikus, mint várt. További információt a [Szintaxis és kifejezések az Azure Resource Manager-sablonokban című témakörben talál.](../../../azure-resource-manager/templates/template-expressions.md)

## <a name="delete-errors"></a>Hibák törlése

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Eset: A hozzárendelés törlésének időmeghosszabbítása

#### <a name="issue"></a>Probléma

A tervrajz-hozzárendelés törlése nem fejeződött be.

#### <a name="cause"></a>Ok

A tervezethozzárendelés törléskor nem terminálállapotban ragadhat meg. Ez az állapot akkor jön létre, ha a tervezet hozzárendelés által létrehozott erőforrások még mindig törlésre várnak, vagy nem ad vissza állapotkódot az Azure Blueprints.This state is caused when resources created by the blueprint assignment are still pending doftion or don't return a status code to Azure Blueprints.

#### <a name="resolution"></a>Megoldás:

A nem terminálállapotú tervezet-hozzárendelések 6 _órás_ időtúltöltés után automatikusan **sikertelennek** vannak jelölve. Miután az időtúlköltség módosította a tervezet hozzárendelésének állapotát, a törlés újra próbálkozható.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
- Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**