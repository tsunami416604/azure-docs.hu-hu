---
title: Identitás és hozzáférés figyelése az Azure Security Centerben | Microsoft Docs
description: Itt megtudhatja, hogyan használható az Azure Security Center identitási és hozzáférési funkciója a felhasználók hozzáférési tevékenységeinek és identitással kapcsolatos problémáinak figyelésére.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 5ee263ef8fb0f20049215eda53e0d58a45342b7e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774828"
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Identitás és hozzáférés figyelése az Azure Security Centerben
Ez a cikk útmutatást nyújt a felhasználók identitási és hozzáférési tevékenységeinek az Azure Security Center segítségével történő figyeléséhez.

## <a name="why-monitor-identity-and-access"></a>Miért fontos az identitás és a hozzáférés figyelése?
Az identitásnak kell lennie a vállalat vezérlősíkjának, és az identitás védelmének kell kapnia a legmagasabb prioritást. Régebben a szervezetek körüli szegélyhálózatok szolgáltak az egyik elsődleges védelmi vonalként. Napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lép a szegélyhálózatok helyére.

Az identitástevékenységek figyelésével proaktív módon tud cselekedni, mielőtt egy incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Az Identitási és hozzáférési irányítópult áttekintést nyújt az identitása állapotáról, beleértve a következőket is:

* A sikertelen bejelentkezési próbálkozások számát. 
* Az ezen próbálkozások során használt felhasználói fiókokat.
* A kizárt fiókokat.
* Azon fiókokat, amelyek jelszavát módosították vagy visszaállították. 
* Az éppen bejelentkezett fiókok számát.

## <a name="monitor-identity-and-access-activities"></a>Identitási és hozzáférési tevékenységek figyelése
Az identitáshoz és a hozzáféréshez kapcsolódó aktuális tevékenységek megjelenítéséhez meg kell nyitnia az **Identitás és hozzáférés** irányítópultot.

1. Nyissa meg a **Security Center** irányítópultját.

2. A bal oldali panel **Megelőzés** területén válassza az **Identitás és hozzáférés** elemet. Ha több munkaterülettel rendelkezik, megjelenik a munkaterület-választó.

    ![Munkaterület kiválasztása](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Ha a jobb szélső oszlopban a **CSOMAG FRISSÍTÉSE** szöveg látható, akkor a munkaterület az ingyenes előfizetést használja. A funkció használatához frissítsen a Standard előfizetésre. Ha a jobb szélső oszlopban a **FRISSÍTÉS SZÜKSÉGES** szöveg látható, frissítse az [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)et a funkció használatához. A díjszabással kapcsolatos további információkért olvassa el a Security Center díjszabását ismertető cikket. 
    > 
3. Ha több munkaterületet szeretne vizsgálni, a **SIKERTELEN BEJELENTKEZÉSEK** oszlop szerint rangsorolhatja a vizsgálatot. Ez az oszlop a munkaterületre való sikertelen bejelentkezési kísérletek aktuális számát jeleníti meg. Válassza ki a használni kívánt munkaterületet, és megjelenik az **Identitás és hozzáférés** irányítópult.

    ![Identitás és hozzáférés](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Az ezen az irányítópulton rendelkezésre álló információk azonnali segítséget nyújtanak a potenciálisan gyanús tevékenységek azonosításához. Az irányítópult három fő területre oszlik:

    a. **Identitás állapota**. Összegzi a munkaterületen végzett, identitáshoz kapcsolódó tevékenységeket.

    b. **Sikertelen bejelentkezések**. Segít gyorsan azonosítani a sikertelen bejelentkezési kísérletek fő okát. Megjeleníti a legtöbb sikertelen bejelentkezést végző tíz fiók listáját.

    c. **Bejelentkezések adott idő alatt**. Segít gyorsan azonosítani az adott idő alatt történt sikertelen bejelentkezési kísérletek számát. Megjeleníti a legtöbb bejelentkezési kísérletet végző fiókok listáját.
    
A kiválasztott csempétől függetlenül a megjelenő irányítópult a naplókeresési lekérdezésen alapul. Az egyetlen különbség a lekérdezés típusa és az eredmény. Egy elemet, például egy számítógépet kiválasztva továbbra is megtekintheti a releváns adatokat. 

## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedett az identitás és a hozzáférés a Security Centerben való figyelésével. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Az Azure Security Center hibaelhárítási útmutatója](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti. 
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

