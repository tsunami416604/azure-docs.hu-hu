---
title: "Az Azure házirend áttekintése |} Microsoft Docs"
description: "Azure házirend egy olyan szolgáltatás, amellyel létrehozása, hozzárendelése és kezelése az Azure környezetben házirend-definíciók, Azure-ban."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 11/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: b784c79166eb614988d84b4553bb95e4d1e457af
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="what-is-azure-policy"></a>Mi az Azure-házirendet?

Informatikai irányítás hoz létre a jobb érthetőség kedvéért bizonyos üzleti céljaihoz és informatikai projektek között. Jó informatikai irányítás magában foglalja a kezdeményezések tervezési és prioritások beállítása a stratégiai szinten. Nem a vállalat informatikai problémák, amelyek soha nem látszólag beolvasása megoldott jelentős számú tapasztalható? Végrehajtási házirendek segítségével jobban kezeléséhez, és megakadályozza, hogy azok. Házirendek megvalósítása Azure házirend honnan.

Az Azure házirend egy olyan szolgáltatás, amellyel létrehozása, hozzárendelése és házirend-definíciók kezelése az Azure-ban. Házirend-definíciók különböző szabályok és kényszerítése műveletek keresztül az erőforrásokat, ezért ezeket az erőforrásokat maradnak megfelel a vállalati szabványoknak és a szolgáltatásszint-szerződések Azure házirend futtatja az erőforrások, azok nem felelnek meg a van a házirend-definíciók keresése értékelését. Például egy házirendet, hogy a virtuális gépek csak bizonyos típusú lehet. Egy másik megköveteli, hogy minden erőforrásokhoz egy adott címkét. Ezek a házirendek majd értékelésének létrehozása és frissítése erőforrásokat.

## <a name="how-is-it-different-from-rbac"></a>Miben különbözik az RBAC?

Házirend és a szerepköralapú hozzáférés-vezérlés (RABC) közötti néhány fontos különbség van. Az RBAC különböző hatóköröket felhasználói műveletek összpontosít. Például akkor lehet, hogy vehető fel a közreműködő szerepkört az egy erőforráscsoport, a kívánt hatókörben. A szerepkör lehetővé teszi, hogy módosíthassa erőforrás csoporthoz. Házirend-ra összpontosít erőforrás-tulajdonságok üzembe helyezése során, és már meglévő erőforrásokat. Például szabályzatokkal, szabályozhatja a telepíthető erőforrások típusú. Vagy a helyek, amelyben az erőforrások kiépítése korlátozhatja. Szerepalapú, eltérően házirend egy alapértelmezett engedélyezése és explicit megtagadja a rendszer.

Házirendek használatára kell hitelesíteni RBAC keresztül. Pontosabban, a fiókot kell-e a:

- `Microsoft.Authorization/policydefinitions/write`engedély házirend definiálásához.
- `Microsoft.Authorization/policyassignments/write`egy házirend hozzárendelése engedély.

Ezek az engedélyek nem szerepelnek a **közreműködő** szerepkör.


## <a name="policy-definition"></a>Házirend-definíció

Minden házirend-definíció van kényszerítve van feltételeket. És történik, ha a feltételek kísérő művelettel rendelkezik.

Azure-szabályzatok fel néhány beépített házirendek, amelyek alapértelmezés szerint elérhető. Példa:

- **Szükséges SQL Server 12.0**: A házirend-definíció van feltételek/szabályok annak érdekében, hogy az összes SQL-kiszolgálók 12.0 verzióját használja-e. A művelet akkor visszautasítja a összes kiszolgáló, amelyek nem felelnek meg ezek a feltételek.
- **Tárolási fiók termékváltozatok engedélyezett**: A házirend-definíció feltételek/szabálykészlet, amely megállapítja, hogy van-e, amelyek telepítése a Termékváltozat-méretét belül van. A művelet akkor visszautasítja a minden kiszolgálón, amely a megadott Termékváltozat-méretét készlete nem igazodik.
- **Erőforrástípus engedélyezett**: A házirend-definíció van egy szabálykészlet feltételek/erőforrás típusát, a szervezet telepíthet. A művelet akkor megtagadni, amely nem tartozik a megadott lista összes erőforrást.
- **Helyek engedélyezett**: Ez a házirend lehetővé teszi korlátozni azon helyeket, amelyek a szervezet adhat meg, amikor erőforrásokat üzembe helyezi. A művelet a földrajzi-megfelelőségi követelményeket kényszerítésére van használatban.
- **Virtuális gép termékváltozatok engedélyezett**: Ez a házirend lehetővé teszi virtuális gépet, a szervezet telepíthet SKU-készletet adjon meg.
- **Alkalmazza a címke és alapértelmezett értékeik**: Ez a házirend vonatkozik egy szükséges kód és az alapértelmezett értéket, ha nincs megadva a felhasználó által.
- **Címke és annak értéke**: Ez a házirend érvénybe lépteti a kötelező címke és annak értékét, hogy egy erőforrás.
- **Nem engedélyezett típusú erőforrások**: Ez a házirend teszi lehetővé, hogy a szervezet nem lehet telepíteni az erőforrás típusát.

Ezek a házirendek az Azure portálon, a PowerShell vagy az Azure parancssori felület használatával tud rendelni.

További információt a házirend-definíciók struktúrák, tekintse meg a cikk - [házirend-definíció szerkezetet](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Házirend-hozzárendelés

Egy házirend-hozzárendelést egy házirend-definíció, belül egy adott hatókörhöz van rendelve. Ebben a hatókörben egy felügyeleti csoport terjedhet az erőforráscsoporthoz. A kifejezés *hatókör* az erőforráscsoportok, előfizetések vagy a házirend-definíció hozzárendelt felügyeleti csoportok hivatkozik. Házirend-hozzárendelések összes gyermek-erőforrás örökli. Ezért ha a házirend vonatkozik egy erőforráscsoport, vonatkozik az erőforráscsoport összes erőforrást. Azonban egy subscope is zárhatja ki a házirend-hozzárendelés. Például a előfizetés hatókörben rendelhet hozzá olyan házirendet, amely megakadályozza, hogy a hálózati erőforrások létrehozását. Azonban kizárhat egy erőforráscsoport, amelyet hálózati infrastruktúra előfizetésen belül. Hozzáférés engedélyezése a felhasználók számára, amelyek megbízható hálózati erőforrások létrehozásának hálózati erőforráscsoporthoz.

A házirend beállításdefiníciókra és hozzárendelések további információkért lásd: [azonosításához az Azure környezetben nem kompatibilis erőforrások házirend-hozzárendelés létrehozása](assign-policy-definition.md).

## <a name="policy-parameters"></a>Házirend-paraméterek

Házirend-paraméterek leegyszerűsíti a Csoportházirend kezelése a házirend-definíciók, létre kell hoznia számának csökkentésével. Paraméterek abba, hogy több általános házirend-definíció létrehozásakor definiálhat. Majd, hogy a házirend-definíció különböző helyzetek kezelésére is felhasználhatja. Ezt megteheti a különböző értékeket történő a házirend-definíció hozzárendelésekor. Például adja meg a helyek az előfizetés egy készletét.

Paraméterek vannak definiálva vagy hozható létre a házirend-definíció létrehozása során. Ha egy paraméter van megadva, nevet és opcionálisan adott érték. Például egy házirend című paraméter adható meg *hely*. Ezt követően rendelhető hozzá eltérő értékek tartoznak, mint *EastUS* vagy *WestUS* a házirend hozzárendelésekor.

<!--
Next link should point to new Concept page for Parameters
-->
Házirend-paraméterekkel kapcsolatos további információkért lásd: [erőforrás-házirendek – áttekintés - paraméterek](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Kezdeményezésére meghatározása
Egy kezdeményezésére definíciója, amely egy szinguláris általános cél elérésére is lefednek házirend-definíciók gyűjteménye. Kezdeményezésére definíciók leegyszerűsíti a kezelése, és rendelje hozzá a házirend-definíciók. A házirendek beállítása egy egyelemű csoportosításával egyszerűsítése érdekében. Például létrehozhat egy című kezdeményezés **figyelés engedélyezése az Azure Security Centerben**, az a célja, hogy az Azure Security Centerben a biztonsági javaslatok figyelése.

Alatt a kezdeményezés például a házirend-definíciók kellene lennie:

1. **A figyelő a Security Center SQL-adatbázis titkosítás nélkül** – titkosítatlan SQL-adatbázisok és a kiszolgálók figyeléséhez.
2. **A Security Center az operációs rendszer biztonsági réseivel figyelése** – kiszolgálók, amelyek nem felelnek meg a beállított alapterv figyelésére.
3. **Hiányzó Endpoint Protection figyelése a Security Center** – hogy a kiszolgálók egy telepített endpoint protection-ügynök nélküli figyelés.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Kezdeményezésére hozzárendelés
Egy házirend-hozzárendelést, például kezdeményezésére hozzárendelése egy-egy adott hatókörhöz rendelt kezdeményezésére definíciója. Kezdeményezésére hozzárendelések csökkenteni kell végeznie néhány kezdeményezésére definíciók minden hatókör. Ebben a hatókörben egy erőforráscsoportba is terjedhet egy felügyeleti csoportban.

Az előző példából a **figyelés engedélyezése az Azure Security Centerben** kezdeményezésére rendelhetők hozzá másik hatókörök. Például egy hozzárendelés rendelhetők hozzá **subscriptionA**. Egy másik rendelhetők hozzá **subscriptionB**.

## <a name="initiative-parameters"></a>Kezdeményezésére paraméterek
Házirend-paraméterek, például kezdeményezésére paraméterek leegyszerűsíti kezdeményezésére felügyeleti redundancia csökkentésével. Kezdeményezésére paraméterei lényegében paramétereket a kezdeményező belül a házirend-definíciók használja listáját.

Például igénybe vehet egy forgatókönyv esetében egy kezdeményezésére definíciója - **initiativeC**, két házirend-definíciók. Minden egyes házirend-definíció paraméter egy meghatározott rendelkezik:

| Szabályzat | a paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| policyA | allowedLocations | A tömb  |Ezt a paramétert értékek karakterláncok listáját vár, mivel a paraméter típusának definiálása tömbként |
| policyB | allowedSingleLocation |Karakterlánc |Ez a paraméter értéket egy word vár, mivel a paraméter típusának definiálása karakterláncként |

Ebben a forgatókönyvben a kezdeményezésére paramétereinek definiálásakor **initiativeC**, három lehetőség közül választhat:

1. A házirend-definíciók belül a kezdeményezés paraméterek: Ebben a példában *allowedLocations* és *allowedSingleLocation* kezdeményezésére paramétereinek válnak **initiativeC** .
2. Adja meg a házirend-definíciók kezdeményezésére meghatározás paramétereinek értékét. A jelen példában adja meg a helyek listáját **policyA tartozó paraméter – allowedLocations** és **policyB tartozó paraméter – allowedSingleLocation**.
Értékek a kezdeményezés hozzárendelésekor is megadhatja.
3. Sorolja fel azokat *érték* e kezdeményezés hozzárendelésekor használható beállítások. A kezdeményezés rendel, a kezdeményező belül a házirend-definíciók örökölt paraméterei csak a megadott listáról értékeket veheti fel.

Például létrehozhat egy kezdeményezésére definícióját tartalmazó érték beállítások listájának *EastUS*, *WestUS*, *CentralUS*, és *WestEurope*. Ha igen, adjon meg egy másik értéket, mint a nem *Délkelet-Ázsia* során a kezdeményezésére hozzárendelés, mert nem szerepel a listán.

## <a name="recommendations-for-managing-policies"></a>Javaslatok házirendek kezelése

Létrehozása és kezelése a házirend-definíciók és hozzárendelések, miközben az alábbiakban néhány mutatók kövesse javasoljuk:

- Házirend-definíciók a környezet létrehozásakor, javasoljuk naplózási hatása, szemben a Megtagadás hatás, a házirend-definíció az erőforrásoknak a környezetben lévő hatásának nyomon követéséhez. Ha vannak olyan parancsprogramjai már, hogy az automatikus skálázás be az alkalmazásokat, egy megtagadási hatás beállítása akadályozhatja a helyen már van feladatok automatizálása.
- Fontos szem előtt tartani szervezeti hierarchiák definíciókat és hozzárendelések létrehozásakor. Javasoljuk, hogy definíciók létrehozása egy magasabb szint, például a felügyeleti csoport vagy az előfizetés szintjén és hozzárendelése a következő gyermek szintjén. Például ha a házirend-definíció a felügyeleti csoport szintjén hoz létre, egy házirend-hozzárendelést adott definíció hatóköre beállítható úgy, egy adott felügyeleti csoporton belüli előfizetés szintre.
- Javasoljuk, hogy a standard tarifacsomag, használatával jobb megértése érdekében a megfelelőségi állapotát a környezetében. További információ a árképzési modellt, és milyen mindegyikhez kínálnak, vessen egy pillantást [árazás](https://azure.microsoft.com/pricing/details/azure-policy).
- Ajánlott mindig kezdeményezésére definíciók helyett a házirend-definíciók, még akkor is, ha csak egy házirend van tartva. Ha a házirend-definíció – például *policyDefA* létrehozása alatt a kezdeményezésére definíció - és *initiativeDefC*, ha úgy dönt, hogy egy másik későbbaházirend-definíciólétrehozása*policyDefB* a hasonló célok *policyDefA*, hozzáadhatja azt a *initiativeDefC* és nyomon követése jobb ily módon.

   Ne feledje, hogy a létrehozása után kezdeményezésére hozzárendelés kezdeményezésére definícióból bármely új házirend-definíciók automatikusan hozzáadja a kezdeményezésére definition visszaállítás alatt, hogy kezdeményezésére definíció kezdeményezésére assignment(s) alatt. Azonban ha egy új paraméter jelenik meg az új házirend-definíció, módosítania kezdeményezésére meghatározására és hozzárendelések a kezdeményezésére definition vagy a hozzárendelés szerkesztésével.

## <a name="next-steps"></a>Következő lépések

Most, hogy Azure házirend áttekintése és az alapvető fogalmakat, azt hogy bevezetéséről némelyike, az alábbiakban a javasolt lépéseket:

- [Rendelje hozzá a házirend-definíció](./assign-policy-definition.md)
- [Rendelje hozzá a házirend-definíció az Azure parancssori felület használatával](./assign-policy-definition-cli.md)
- [Rendelje hozzá a házirend-definíció PowerShell használatával](./assign-policy-definition-ps.md)
