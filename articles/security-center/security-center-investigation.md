---
title: Incidensek és riasztások vizsgálata az Azure Security Centerben | Microsoft Docs
description: Ez a dokumentum segítséget nyújt a biztonsági incidensek és riasztások vizsgálatához az Azure Security Center vizsgálati funkciójának használatával.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 6ba21c6eacd5b72d13706c08f0cc9883ccc91388
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107474"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Incidensek és riasztások vizsgálata az Azure Security Centerben (előzetes verzió)
Ez a dokumentum segítséget nyújt a biztonsági incidensek és riasztások vizsgálatához az Azure Security Center vizsgálati funkciójának használatával.

## <a name="what-is-investigation-in-security-center"></a>Mit jelent a vizsgálat a Security Centerben?
A Security Center Vizsgálat szolgáltatásával osztályozhatja a potenciális [biztonsági incidenseket](https://docs.microsoft.com/azure/security-center/security-center-incident), feltérképezheti a hatókörüket, és azonosíthatja a kiváltó okokat.

A szolgáltatás célja, hogy megkönnyítse a vizsgálati eljárást a vizsgált incidensben érintett összes entitás ([biztonsági riasztások](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), felhasználók, számítógépek és incidensek) összekapcsolásával.  A Security Center ezt azáltal éri el, hogy összegyűjti és egymáshoz viszonyítja az összes érintett entitásra vonatkozó adatokat, és ezeket a kölcsönviszonyokat megjeleníti egy élő gráfon, amelynek segítéségével eligazodhat az egyes objektumok között, és megjelenítheti a vonatkozó információkat.


> [!NOTE]
> * [Egyéni riasztások](security-center-custom-alert.md) a Security Center vizsgálati funkciójáról nem támogatottak.
> * Vizsgálat csak a támogatott Windows kiszolgálókról összegyűjtött adatokon alapuló riasztások.


## <a name="how-investigation-works"></a>A vizsgálat működése
A vizsgálat alapját a vizsgálati irányítópult középső területén megjelenő gráf adja. A gráfon mindig valamely adott entitás van a középpontban, és az ahhoz viszonyítva jeleníti meg a kapcsolódó entitásokat. Az entitások lehetnek biztonsági riasztások, felhasználók, számítógépek vagy incidensek.

![Térkép](./media/security-center-investigation/security-center-investigation-fig1.png)

A felhasználó az entitások között úgy navigálhat, ha rájuk kattint a gráfon. A gráf automatikusan a kiválasztott entitásra és annak kapcsolt entitásaira fókuszál. A már nem releváns entitások eltávolíthatók a gráfról.

### <a name="investigation-path"></a>Vizsgálati útvonal
Ahogy a felhasználó a különböző entitások között lépked, a vizsgálati útvonal nyilvántartja a vizsgálati környezetet, és segít a gyors eligazodásban. A vizsgálat eredményeit tartalmazó incidens mindig a vizsgálati útvonal legelső (bal szélső) eleme.

![Útvonal](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Általános információk
A gráfon látható entitásokról a füleken további információk jelennek meg. Az **Információ** lap az entitással kapcsolatos, különböző adatforrásokból származó általános információkat tartalmaz.

![Általános információk](./media/security-center-investigation/security-center-investigation-fig3.png)

Az információs lap a gráfon kiválasztott incidensre vonatkozó információkat mutat. Az incidens egy tároló, amely egy adott vizsgálat eredményeit tartalmazza. Minden vizsgálatra valamely incidens kontextusában kerül sor.

Az incidens létrehozásához a felhasználónak a **Vizsgálat indítása** gombra kell kattintania egy adott riasztásban. A vizsgáló számára elérhető alapvető funkció a különféle entitások, például felhasználók, számítógépek vagy riasztások megjelölése. A kapcsolódó entitások megjelölésekor meg kell adni egy okot. Ettől a pillanattól kezdve az entitás közvetlenül az incidens alatt jelenik meg a gráfon és az entitások listájában.

### <a name="entities"></a>Entitások

Az **Entitások** lapon a kapcsolódó entitások jelennek meg, típus szerint csoportosítva. Ez két esetben lehet hasznos: ha túl sok az entitás ahhoz, hogy gráfon meg lehessen jeleníteni őket, illetve ha az entitások neve túl hosszú, így egyszerűbb a táblázatos megjelenítés.

![Entitások](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Keresés

A **Keresés** lapon az entitáshoz elérhető naplótípusok jelennek meg. Minden egyes naplótípusnál látható, hogy hány rekordok tartalmaz. Az egyes naplótípusokra kattintva a keresési képernyőre léphet. A keresési képernyőn finomíthatja a keresést, és használhatja a különféle keresési szolgáltatásokat, például riasztásokat állíthat be. Az aktuális kiadásban a keresési lap csak felhasználó és számítógép entitások esetében érhető el.

![Keresés](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Feltárás

A **Feltárás** lapon a vizsgáló az entitáshoz kapcsolódó különféle hibákra vonatkozó adatokat vizsgálhatja meg. Például egy gép vizsgálatakor a rajta végrehajtott folyamatok listája jelenik meg a Feltárás lapon. Bizonyos esetekben a Feltárás lapon megjelenő adatok esetleg gyanús hibákat jelezhetnek. A vizsgáló megvizsgálhatja az adatokat a lapon, vagy megnyithatja őket a keresési képernyőn a nagyobb adatkészletek vizsgálatához, illetve a speciális keresési beállítások, például a szűrés és az Excelbe való exportálás használatához.

![Feltárás](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Idővonal

A gráfon és a különféle füleken megjelenő adatok zöme egy adott időszakra vonatkozik. Az időszak az időszakválasztón állítható be a gráf bal oldalán. Különféle módszerek állnak a vizsgáló rendelkezésére az időszak kiválasztásához.

![Idővonal](./media/security-center-investigation/security-center-investigation-fig7.png)

A kiválasztott időszaktól az alábbi elemek függnek:

- A gráf felhasználó–számítógép összefüggései: csak azok a felhasználók jelennek meg, akik az adott időszakon belül jelentkeztek be a számítógépekre.
- A számítógépek és a felhasználók vizsgálata során jelzett riasztások: csak az adott időszakon belül bekövetkezett riasztások jelennek meg.
- Az entitások lapja a gráfhoz hasonló logika mentén működik.

Az alábbi elemek megjelenítése a kiválasztott időszaktól független:

- Ha a rendszer megjelenít egy riasztást, az abban érintett entitások, például felhasználók és számítógépek minden esetben megjelennek.
- Ha egy incidens tartalmaz egy adott entitást, az mindig megjelenik.

> [!NOTE]
> A **Keresés** és a **Feltárás** lapokon csak az adott időszakra vonatkozó rekordok jelennek meg.

## <a name="how-to-perform-an-investigation"></a>Vizsgálat végrehajtása

A vizsgálatok biztonsági incidensből vagy riasztásból indíthatók, attól függően, hogy mi felel meg jobban az igényeinek. A következő lépéseket egy vizsgálat indítása riasztásból használhatók:

1.  Nyissa meg a **Security Center** irányítópultját.
2.  Kattintson a **Biztonsági riasztások** gombra, és válassza ki a kivizsgálni kívánt incidenst.
3.  Az incidens lapján kattintson a **Vizsgálat indítása** gombra a **Vizsgálat** irányítópult megjelenítéséhez.

    ![Riasztás](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Ha ezen az irányítópulton kiválaszt egy entitást a gráfon, a vonatkozó adatai megjelennek a képernyő jobb oldalán.

    ![Vizsgálat irányítópult](./media/security-center-investigation/security-center-investigation-fig9.png)

Innen feltárhatja az adott incidensben érintett entitásokat, és felderítheti a velük kapcsolatos további adatokat.

## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett a Security Center vizsgálati funkcióinak használatával. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
