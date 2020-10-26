---
title: Azure-foglalások kihasználtságával kapcsolatos hibák elhárítása
description: Ez a cikk segítséget nyújt azokkal az Azure-foglalásokkal kapcsolatos problémák megértéséhez és elhárításához, amelyeknél nem látható a kihasználtság, vagy pedig nulla (0) kihasználtság látható az Azure Portalon. A cikk a nem egyező kihasználtsági értékek okát is ismerteti.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207433"
---
# <a name="troubleshoot-reservation-utilization"></a>Foglalási kihasználtság hibaelhárítása

Ez a cikk segítséget nyújt azokkal az Azure-foglalásokkal kapcsolatos problémák megértéséhez és elhárításához, amelyeknél nem látható a kihasználtság, vagy pedig nulla (0) kihasználtság látható az Azure Portalon. A cikk a nem egyező kihasználtsági értékek okát is ismerteti.

## <a name="symptoms"></a>Hibajelenségek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és lépjen a **Foglalások** területre.
1. A foglalások listájában a **Kihasználtság (%)** oszlopban láthatja a foglalások kihasználtságának mértékét. Ennek értéke lehet 0%.
1. Válassza ki a foglalást.
1. Előfordulhat, hogy a foglalás áttekintési oldalának grafikonján a használatra vonatkozó százalékos érték nem egyezik meg a foglalások listájában látható értékkel.

## <a name="cause"></a>Ok

Az Azure Portal **Kihasználtság (%)** oszlopa az aktuális napra vonatkozó értéket jeleníti meg. Az érték számítása akkor történik, amikor megtörténik a használati adatok fogadása onnan, ahol az erőforrások futnak. Az Azure a használati adatok segítségével számítja ki a kihasználtság százalékos értékét.

Egyes erőforrások a többinél lassabban jelentik a használatot. Emellett bizonyos terméktípusok, például az SQL Database-adatbázisok lassan jelentik a használati adataikat.

A késés miatt a kihasználtság számítása a tényleges használatnál alacsonyabb értékeket jeleníthet meg. Az eltérés a napok határánál látható. Ilyen esetekben, ha az Azure négy–nyolc óráig nem kap használati adatokat, 0%-os értéket számít. A 0%-os érték azért jelenik meg, mert a használati adatok nem érkeztek meg, és úgy tűnik, hogy a foglalás nem alkalmaz előnyt az erőforrásokra vonatkozóan.

Ahogy a használati adatok fogadása megtörténik, az érték a megfelelő százalék irányába változik. Az összes használati adat összegyűjtését követően a rendszer meghatározza a helyes értéket, és pontosan megjeleníti a grafikonon.

## <a name="solution"></a>Megoldás

Ha azt tapasztalja, hogy a kihasználtsági értékek nem felelnek meg az elvárásainak, tekintse meg a grafikont a tényleges kihasználtság lehető legpontosabb felméréséhez. A két napnál régebbi pontértékeknek pontosaknak kell lenniük. A héttől 30 napig terjedő időszakra vonatkozó hosszútávú átlagoknak szintén pontosaknak kell lenniük.

## <a name="next-steps"></a>Következő lépések

- A foglalások kezelésével kapcsolatos további információkért lásd: [Foglalások kezelése az Azure-erőforrásoknál](manage-reserved-vm-instance.md).