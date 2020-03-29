---
title: Hibaelhárítás - Personalizer
description: Ez a cikk a Personalizer-re vonatkozó gyakori hibaelhárítási kérdésekre adott válaszokat tartalmazza.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336031"
---
# <a name="personalizer-troubleshooting"></a>Személyre szabásával kapcsolatos hibaelhárítás

Ez a cikk a Personalizer-re vonatkozó gyakori hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="transaction-errors"></a>Tranzakciós hibák

<details>
<summary><b>Http 429-es (túl sok kérés) választ kapok a szolgáltatástól. Mit tehetnék?</b></summary>

**Válasz:** Ha a Personalizer példány létrehozásakor ingyenes árszintet választott, az engedélyezett Rangsorolási kérelmek számára vonatkozó kvótakorlát van. Tekintse át az API-hívási sebességet a Rank API-hoz (az Azure Portal metrika ablaktáblájában a Personalizer-erőforráshoz), és módosítsa a tarifacsomagot (a tarifacsomag ablaktáblában), ha a hívásmennyisége várhatóan meghaladja a kiválasztott tarifacsomag küszöbértékét.

</details>

<details>
<summary><b>Kapok egy 5xx hiba rang vagy jutalom API-k. Mit kell tennem?</b></summary>

**Válasz**: Ezeknek a kérdéseknek átláthatónak kell lenniük. Ha folytatják, lépjen kapcsolatba az ügyfélszolgálattal az **Új támogatási kérelem** kiválasztásával a Támogatás + **hibaelhárítás** szakaszban, az Azure Portalon a Personalizer erőforráshoz.

</details>

## <a name="learning-loop"></a>Tanulási ciklus

<details>
<summary>
<b>A tanulási hurok nem éri el a 100%-os egyezést a rendszer nélkül Personalizer. Hogyan tudom ezt helyrehozni?</b></summary>

**Válasz**: Az okok nem éri el a célt a tanulási hurok:
* Nincs elég szolgáltatás, amelyet A Rang API-hívással küldtünk
* Hibák az elküldött funkciókban – például nem összesített szolgáltatásadatok, például időbélyegek küldése a Rank API-ba
* Hibák a hurok feldolgozásával - például nem küld jutalomadatokat a Reward API-nak az eseményekért

A javításhoz meg kell változtatnia a feldolgozást a hurokba küldött funkciók módosításával, vagy győződjön meg arról, hogy a jutalom a Rang válaszának minőségének helyes értékelése.

</details>

<details>
<summary>
<b>A tanulási hurok nem úgy tűnik, hogy tanulni. Hogyan tudom ezt helyrehozni?</b></summary>

**Válasz:** A tanulási huroknak néhány ezer jutalomhívásra van szüksége, mielőtt a Rank hívások hatékonyan rangsorolnának.

Ha nem biztos abban, hogy a tanulási ciklus jelenleg hogyan viselkedik, futtasson [offline értékelést,](concepts-offline-evaluation.md)és alkalmazza a javított tanulási szabályzatot.

</details>

<details>
<summary><b>Mindig egyre rangot eredmények et minden azonos valószínűségek minden elem. Honnan tudhatom, hogy a Personalizer tanul?</b></summary>

**Válasz:** Personalizer ugyanazt a valószínűségeket adja vissza a Rank API-eredmény, ha csak most kezdődött el, és egy _üres_ modell, vagy amikor alaphelyzetbe állítja a Personalizer Loop, és a modell továbbra is a **modell frissítési gyakorisági** időszakon belül.

Amikor az új frissítési időszak megkezdődik, a frissített modell thasználja, és látni fogja a valószínűségek változását.

</details>

<details>
<summary><b>A tanulási hurok volt a tanulás, de úgy tűnik, hogy nem tanulnak többé, és a minősége a Rank eredmények nem olyan jó. Mit kell tennem?</b></summary>

**Válasz**:
* Győződjön meg arról, hogy elvégezte és alkalmazta az Azure Portalon az adott Personalizer erőforrás (tanulási hurok) kiértékelését.
* Győződjön meg róla, hogy minden jutalom elküldésre kerül a Jutalom API-n keresztül, és feldolgozza.

</details>


<details>
<summary><b>Honnan tudhatom, hogy a tanulási hurok rendszeresen frissül, és az adataim pontozására szolgál?</b></summary>

**Válasz:** A modell legutóbbi frissítésének idejét az Azure Portal **Modell és tanulási beállítások** lapján találhatja meg. Ha egy régi időbélyeget lát, annak valószínűleg az az oka, hogy nem a Rang és jutalom hívásokat küldi. Ha a szolgáltatás nem rendelkezik bejövő adatokkal, nem frissíti a tanulást. Ha látja, hogy a tanulási hurok nem frissül elég gyakran, szerkesztheti a hurok **modellfrissítési gyakoriságát.**

</details>

## <a name="offline-evaluations"></a>Offline értékelések

<details>
<summary><b>A kapcsolat nélküli értékelés funkciófontossága hosszú listát ad vissza több száz vagy több ezer elemet tartalmazó listával. mi történt?</b></summary>

**Válasz:** Ez általában időbélyegek, felhasználói azonosítók vagy más, részletesen küldött funkciók miatt van.

</details>

<details>
<summary><b>Csináltam egy offline értékelést, és sikerült szinte azonnal. Miért van ez? Nem látok semmilyen eredményt?</b></summary>

**Válasz:** Az offline kiértékelés az adott időszakban betanított modelladatokat használja. Ha az értékelés kezdési és befejezési időpontja közötti időszakban nem küldött adatokat, az eredmény nélkül befejeződik. Küldjön be egy új offline kiértékelést egy időtartomány kiválasztásával, amely tartalmazza azokat az eseményeket, amelyekről tudja, hogy elküldték a Personalizer-nek.

</details>


## <a name="learning-policy"></a>Tanulási szabályzat

<details>
<summary><b>Hogyan importálhatok tanulási szabályzatot?</b></summary>

**Válasz:** További információ a [szabályzatok megismeréséről](concept-active-learning.md#understand-learning-policy-settings) és az új tanulási szabályzat [alkalmazásáról.](how-to-manage-model.md) Ha nem szeretne tanulási szabályzatot választani, az [offline értékelés](how-to-offline-evaluation.md) segítségével az aktuális események alapján tanulási szabályzatot javasolhat.

</details>

## <a name="security"></a>Biztonság

<details>
<summary><b>A hurok API-kulcsa sérült. Mit tehetnék?</b></summary>

**Válasz:** Az egyik kulcsot újragenerálhatja, miután az ügyfeleket a másik kulcs használatára cserélte. Miután két gomb lehetővé teszi, hogy propagálja a kulcsot egy lusta módon, anélkül, hogy bármilyen állásidő. Azt javasoljuk, hogy ezt a rendszeres ciklus, mint biztonsági intézkedés.

</details>

## <a name="next-steps"></a>További lépések

[A modellfrissítési gyakoriság konfigurálása](how-to-settings.md#model-update-frequency)