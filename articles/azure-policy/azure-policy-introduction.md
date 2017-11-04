---
title: "Az Azure házirend áttekintése |} Microsoft Docs"
description: "Azure házirend egy olyan szolgáltatás, amellyel létrehozása, hozzárendelése és kezelése az Azure környezetben házirend-definíciók, Azure-ban."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>Mi az Azure-házirendet?

Informatikai irányítás hoz létre a jobb érthetőség kedvéért bizonyos üzleti céljaihoz és informatikai projektek között. Jó informatikai irányítás magában foglalja a kezdeményezések tervezési és prioritások beállítása a stratégiai szinten. Ha a vállalata informatikai problémák, amelyek soha nem tűnik, hogy az beszerzése feloldva, végrehajtási házirendek segítségével jobban jelentős számú kezeléséhez, és megakadályozza, hogy azok. Ez azért, ahol Azure házirend érkezik.

Az Azure házirend egy olyan szolgáltatás, amellyel létrehozása, hozzárendelése és házirend-definíciók kezelése az Azure-ban. Házirend-definíciók különböző szabályok és kényszerítése műveletek keresztül az erőforrásokat, ezért ezeket az erőforrásokat maradnak megfelel a vállalati szabványoknak és a szolgáltatásszint-szerződések Ennek érdekében fut, a erőforrások vizsgálata, amelynek azokat, amelyek nem felelnek már működik a házirend-definíciók értékelését.

## <a name="policy-definition"></a>Házirend-definíció

Minden házirend-definíció van feltételeit úgy jön létre, és az, hogy a hozzá tartozó műveletet helyezze el, ha a feltételek teljesülnek.

Azure-szabályzatok fel néhány beépített házirendek, amelyek alapértelmezés szerint elérhető. Példa:

- **Szükséges SQL Server 12.0**: A házirend-definíció van feltételek/szabályok annak érdekében, hogy az összes SQL-kiszolgálók 12.0 verzióját használja-e. A művelet akkor visszautasítja a összes kiszolgáló, amelyek nem felelnek meg ezek a feltételek.
- **Tárolási fiók termékváltozatok engedélyezett**: A házirend-definíció feltételek/szabálykészlet, amely megállapítja, hogy van-e, amelyek telepítése a Termékváltozat-méretét belül van. A művelet akkor visszautasítja a minden kiszolgálón, amely a megadott Termékváltozat-méretét készlete nem igazodik.
- **Erőforrástípus engedélyezett**: A házirend-definíció van egy szabálykészlet feltételek/erőforrás típusát, a szervezet telepíthet. A művelet akkor megtagadni, amely nem tartozik a megadott lista összes erőforrást.

További információt a házirend-definíciók struktúrák, tekintse meg a cikk - [házirend-definíció szerkezetet](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Házirend-hozzárendelés
Egy házirend-hozzárendelést egy házirend-definíció, belül egy adott hatókörhöz van rendelve. Ebben a hatókörben egy felügyeleti csoport terjedhet az erőforráscsoporthoz.

A házirend beállításdefiníciókra és hozzárendelések további információkért lásd: [erőforrás házirendek – áttekintés](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>Házirend-paraméterek
Házirend-paraméterek leegyszerűsíti a Csoportházirend kezelése a házirend-definíciók, létre kell hoznia számának csökkentésével. Paraméterek abba, hogy több általános házirend-definíció létrehozásakor definiálhat. Majd használni, hogy a házirend-definíció különböző helyzetek kezelésére sikeres különböző értékeket (például megadják az előfizetéshez tartozó helyek egy set) Ha a házirend hozzárendelése.

Paraméterek vannak definiálva vagy hozható létre a házirend-definíció létrehozása során. Ha egy paraméter van megadva, nevet és opcionálisan adott érték. Például képes határozzon meg egy házirend paramétert olyan hely, és adja meg az eltérő értékek tartoznak, mint *EastUS* vagy *WestUS* a házirend hozzárendelésekor.

Házirend-paraméterekkel kapcsolatos további információkért lásd: [erőforrás-házirendek – áttekintés - paraméterek](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Kezdeményezésére meghatározása
Egy kezdeményezésére definíciója, amely egy szinguláris általános cél elérésére is lefednek házirend-definíciók gyűjteménye. Például létrehozhat egy című kezdeményezés **figyelés engedélyezése az Azure Security Centerben**, az a célja, hogy az Azure Security Centerben a biztonsági javaslatok figyelése.

Alatt a kezdeményezés például a házirend-definíciók kellene lennie:

1. **A figyelő a Security Center SQL-adatbázis titkosítás nélkül** – titkosítatlan SQL-adatbázisok és a kiszolgálók figyeléséhez.
2. **A Security Center az operációs rendszer biztonsági réseivel figyelése** – kiszolgálók, amelyek nem felelnek meg a beállított alapterv figyelésére.
3. **Hiányzó Endpoint Protection figyelése a Security Center** – hogy a kiszolgálók egy telepített endpoint protection-ügynök nélküli figyelés.

## <a name="initiative-assignment"></a>Kezdeményezésére hozzárendelés
Egy házirend-hozzárendelést, például kezdeményezésére hozzárendelése egy-egy adott hatókörhöz rendelt kezdeményezésére definíciója. Kezdeményezésére hozzárendelések csökkenteni kell végeznie néhány kezdeményezésére definíciók minden hatókör. Ebben a hatókörben egy erőforráscsoportba is terjedhet egy felügyeleti csoportban.

Az előző példából a **figyelés engedélyezése az Azure Security Centerben** kezdeményezésére rendelhetők hozzá másik hatókörök. Például egy hozzárendelés rendelhetők hozzá **subscriptionA**, míg a másik rendelhetők hozzá **subscriptionB**.

## <a name="initiative-parameters"></a>Kezdeményezésére paraméterek
Házirend-paraméterek, például kezdeményezésére paraméterek leegyszerűsíti kezdeményezésére felügyeleti redundancia csökkentésével. Kezdeményezésére paraméterei lényegében paramétereket a kezdeményező belül a házirend-definíciók használja listáját.

Például igénybe vehet egy forgatókönyv esetében egy kezdeményezésére definíciója - **initiativeC**, két házirend-definíciók. Minden egyes házirend-definíció paraméter egy meghatározott rendelkezik:

|Szabályzat  |a paraméter neve     |Paraméter típusa  |Megjegyzés                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |A tömb  |Ezt a paramétert értékek karakterláncok listáját vár, mivel a paraméter típusának definiálása tömbként |
|policyB |allowedSingleLocation |Karakterlánc |Ez a paraméter értéket egy word vár, mivel a paraméter típusának definiálása karakterláncként          |

Ebben a forgatókönyvben a kezdeményezésére paramétereinek definiálásakor **initiativeC**, három lehetőség közül választhat:

1. Kihasználhatja a kezdeményezés belül a házirend-definíciók paraméterei: Ebben a példában *allowedLocations* és *allowedSingleLocation* kezdeményezésére paramétereinek válnak  **initiativeC**.
2. Adja meg a házirend-definíciók kezdeményezésére meghatározás paramétereinek értékét. A jelen példában adja meg a helyek listáját **policyA tartozó paraméter – allowedLocations** és **policyB tartozó paraméter – allowedSingleLocation**.
Értékek a kezdeményezés hozzárendelésekor is megadhatja.
3. Sorolja fel azokat *érték* e kezdeményezés hozzárendelésekor használható beállítások. Ez azt jelenti, hogy a kezdeményezés rendel, a kezdeményező belül a házirend-definíciók örökölt paraméterei csak értékeket veheti fel a megadott listáról.

Például ha az érték a megadott listája lehetőségek a kezdeményezésére definíció létrehozása során, rendelkezik – *EastUS*, *WestUS*, *CentralUS*, és *WestEurope* , nem lehet egy másik értéket, mint a felhasználótól, *Délkelet-Ázsia* során a kezdeményezésére hozzárendelés, mert nem szerepel a listán.

## <a name="recommendations-for-managing-policies"></a>Javaslatok házirendek kezelése

Létrehozása és kezelése a házirend-definíciók és hozzárendelések, miközben az alábbiakban néhány mutatók kövesse javasoljuk:

- Házirend-definíciók a környezet létrehozásakor, javasoljuk egy naplózási hatása, szemben a Megtagadás hatás tartása nyomon követése a házirend-definíció hatását a környezetben. Ha vannak olyan parancsprogramjai már, hogy az automatikus skálázás be az alkalmazásokat, egy megtagadási hatás beállítása akadályozhatja a helyen már van feladatok automatizálása.
- Fontos szem előtt tartani szervezeti hierarchiák definíciókat és hozzárendelések létrehozásakor. Javasoljuk, hogy definíciók létrehozása egy magasabb szint, például a felügyeleti csoport vagy az előfizetés szintjén és hozzárendelése a következő gyermek szintjén. Például ha a házirend-definíció a felügyeleti csoport szintjén hoz létre, egy házirend-hozzárendelést adott definíció hatóköre beállítható úgy, egy előfizetés szintre.
- Javasoljuk, hogy a standard tarifacsomag, használatával jobb megértése érdekében a megfelelőségi állapotát a környezetében.
- Ajánlott mindig kezdeményezésére definíciók helyett a házirend-definíciók, még akkor is, ha csak egy házirend van tartva. Ha a házirend-definíció – például *policyDefA* létrehozása alatt a kezdeményezésére definíció - és *initiativeDefC*, ha úgy dönt, hogy hozzon létre egy másik házirend-definíció például, hogy a célok *policyDefA*, egyszerűen hozzáadhatja a *initiativeDefC* és nyomon követése jobb ily módon.

   Ne feledje, hogy automatikusan összesítésére alapján, hogy kezdeményezésére definíció kezdeményezésére assignment(s) a létrehozása után kezdeményezésére hozzárendelés kezdeményezésére definícióból kezdeményezésére definíciójának hozzáadott bármely új házirend-definíciók. Azonban ha egy új paraméter jelenik meg az új házirend-definíció, módosítania kezdeményezésére meghatározására és ennek megfelelően a definícióban vagy a hozzárendelés szerkesztésével hozzárendeléseket.

## <a name="next-steps"></a>Következő lépések:
Most, hogy Azure házirend áttekintése és az alapvető fogalmakat, azt hogy bevezetéséről némelyike, az alábbiakban a javasolt lépéseket:

- [Rendelje hozzá a házirend-definíció](./assign-policy-definition.md)
- [Rendelje hozzá a házirend-definíció az Azure parancssori felület használatával](./assign-policy-definition-cli.md)
- [Rendelje hozzá a házirend-definíció PowerShell használatával](./assign-policy-definition-ps.md)
