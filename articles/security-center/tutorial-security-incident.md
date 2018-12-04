---
title: Azure Security Center oktatóanyag – Reagálás a biztonsági incidensekre | Microsoft Docs
description: Azure Security Center oktatóanyag – Reagálás a biztonsági incidensekre
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: facea1f0c9c92a07d888163cc44f67d927698002
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849615"
---
# <a name="tutorial-respond-to-security-incidents"></a>Oktatóanyag: Reagálás a biztonsági incidensekre
A Security Center fejlett elemzési eszközök és fenyegetésfelderítés segítségével folyamatosan elemzi a hibrid felhőbeli számítási feladatokat, hogy figyelmeztesse az esetleges rosszindulatú tevékenységekre. Ráadásul a Security Centerbe más biztonsági termékekből és szolgáltatásokból is integrálhat riasztásokat, és egyéni riasztásokat is létrehozhat a saját mutatói és intelligens forrásai alapján. Amikor valami kivált egy riasztást, gyors reagálásra van szükség a problémák kivizsgálásához és elhárításához. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Biztonsági riasztások osztályozása
> * Részletes vizsgálat egy biztonsági incidens gyökerének és kiterjedésének meghatározása céljából
> * Keresés a biztonsági adatok között a vizsgálat elősegítéséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban ismertetett funkciók végrehajtásához a Security Center Standard tarifacsomagjával kell rendelkeznie. Megpróbálhatja Security Center Standard költségek nélkül. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](security-center-get-started.md) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

## <a name="triage-security-alerts"></a>Biztonsági riasztások osztályozása
A Security Center segítségével minden biztonsági riasztást egy egyesített nézetben tekinthet meg. A biztonsági riasztások besorolása a súlyosságon alapul, és ahol lehetséges, a kapcsolódó riasztásokat a rendszer egyetlen biztonsági incidensben egyesíti. A riasztások és események osztályozásakor a következő feladatai vannak:

- További műveleteket nem igénylő riasztások elvetése, ha például a riasztás téves
- Ismert támadások elhárítása, például rosszindulatú IP-címről érkező hálózati forgalom blokkolása
- További vizsgálatot igénylő riasztások meghatározása


1. A Security Center főmenüjének **ÉSZLELÉS** területén válassza a **Biztonsági riasztások** elemet:

  ![Biztonsági riasztások](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)  

2. A riasztások listájában kattintson egy biztonsági incidensre (egy riasztásgyűjteményre) az esemény részleteinek megjelenítéséhez. Megnyílik **A rendszer biztonsági incidenst észlelt** ablak.

  ![Biztonsági incidens](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Ezen a képernyőn felül megtalálja a biztonsági incidens leírását, majd azon riasztások listáját, amelyek az esemény részeit képezik. További információért kattintson az alaposabban megvizsgálni kívánt riasztásra.

  ![Biztonsági incidens](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

  A riasztások típusa eltérő lehet. A riasztási típusokkal és az elhárításhoz szükséges lehetséges lépésekkel kapcsolatban tekintse meg [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) című cikket. A biztonságosan elvethető riasztások esetében jobb gombbal a riasztásra kattinthat, majd kiválaszthatja az **Elvetés** lehetőséget:

  ![Riasztás](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Ha a hiba gyökere és a rosszindulatú tevékenység hatóköre nem ismert, további vizsgálatért lépjen a következő lépésre.

## <a name="investigate-an-alert-or-incident"></a>Egy riasztás vagy esemény vizsgálata
1. A **Biztonsági riasztás** oldalon kattintson a **Vizsgálat indítása** gombra (ha már megkezdte a vizsgálatot, a gomb neve **Vizsgálat folytatása** lesz).

  ![Vizsgálat](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

  A vizsgálati térkép a biztonsági riasztáshoz vagy eseményhez kapcsolódó entitások grafikus megjelenítése. Ha a térkép egy entitására kattint, az arra vonatkozó információk új entitásokként jelennek meg, és a térkép egyre bővül. A térképen kiválasztott entitás tulajdonságai kiemelve jelennek meg az oldal jobb oldalán található panelen. Az egyes lapokon megjelenő információk a kiválasztott entitástól függően változnak. A vizsgálati folyamat során a támadó lépéseinek jobb megértése érdekében tekintse át az összes kapcsolódó információt.

2. Ha több bizonyítékra van szüksége, vagy további vizsgálatra van szükség a talált entitásokkal kapcsolatban, lépjen a következő lépésre.

## <a name="search-data-for-investigation"></a>Keresés az adatok között a vizsgálathoz

A Security Center keresési képességeinek segítségével további bizonyítékokat kereshet a sérült rendszerekről, valamint további részleteket tárhat fel a vizsgálat tárgyát képező entitásokról.

Keresés indításához nyissa meg a **Security Center** irányítópultot, kattintson a bal oldali navigációs ablaktáblán a **Keresés** elemre, válassza ki a keresni kívánt entitásokat tartalmazó munkaterületet, írja be a keresési lekérdezést, majd kattintson a Keresés gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, továbbra is a Standard szintet futtassa, és hagyja engedélyezve az automatikus kiépítést. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést vagy a szabályzatot, amelyet vissza szeretne állítani Ingyenes állapotba. Megnyílik a **Biztonsági szabályzat** képernyő.
3. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Tarifacsomag** lehetőséget.
4. Válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az automatikus kiépítés letiltásához a **Biztonsági szabályzat – Adatgyűjtés** területen válassza a **Ki** lehetőséget az **Előkészítés** elemnél.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agentet azon Azure-beli virtuális gépekről, ahol az ügynök üzembe lett helyezve. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a Security Center azon funkcióit ismerte meg, amelyeket a rendszer egy biztonsági incidensre adott reakció során használ, például:

> [!div class="checklist"]
> * Biztonsági incidens, amely egy erőforráshoz tartozó összes riasztás együttese
> * Vizsgálati térkép, amely a biztonsági riasztáshoz vagy eseményhez kapcsolt entitások grafikus megjelenítése
> * Keresési képességek a sérült rendszerekre vonatkozó további bizonyítékok kereséséhez

További információk a Security Center vizsgálati funkciójáról:

> [!div class="nextstepaction"]
> [Incidensek és riasztások vizsgálata](security-center-investigation.md)
