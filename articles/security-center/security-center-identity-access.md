---
title: "Identitás és hozzáférés figyelése az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum útmutatást nyújt a felhasználók hozzáférési tevékenysége és identitással kapcsolatos problémái az Azure Security Center identitási és hozzáférési funkcióival történő figyeléséhez."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: hu-hu
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Identitás és hozzáférés figyelése az Azure Security Centerben
Ez a dokumentum útmutatást nyújt a felhasználók identitási és hozzáférési tevékenységeinek az Azure Security Center segítségével történő figyeléséhez.

## <a name="why-monitor-identity-and-access"></a>Miért fontos az identitás és a hozzáférés figyelése?
Az identitásnak kell lennie a vállalat vezérlősíkjának, és az identitás védelmének kell kapnia a legmagasabb prioritást. Míg a múltban a szervezetek körüli szegélyhálózatok szolgáltak az egyik elsődleges védelmi vonalként, napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lépett a szegélyhálózatok helyére.

Az identitástevékenységek figyelésével proaktívan fog tudni cselekedni, mielőtt az incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Az Identity and Access (Identitás és hozzáférés) irányítópult áttekintést biztosít az identitás állapotáról, többek között a sikertelen bejelentkezési kísérletekről, a kísérletekhez használt felhasználói fiókról, a kizárt fiókokról, a módosított vagy visszaállított jelszavú fiókokról, valamint az aktuálisan bejelentkezett fiókok számáról.

## <a name="how-to-monitor-identity-and-access-activities"></a>Hogyan figyelhetők meg az identitási és hozzáférési tevékenységek?
Kövesse az alábbi lépéseket az identitáshoz és a hozzáféréshez kapcsolódó aktuális tevékenységek megjelenítéséhez. Ehhez meg kell nyitnia az **Identitás és hozzáférés** irányítópultot:

1.  Nyissa meg a **Security Center** irányítópultját.
2.  A bal oldali panel **Megelőzés** területén kattintson az **Identitás és hozzáférés** lehetőségre. Ha több munkaterülettel rendelkezik, megjelenik a munkaterület-választó.

    ![munkaterület kiválasztása](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Ha az utolsó oszlopban a **CSOMAG FRISSÍTÉSE** szöveg látható, akkor a munkaterület az ingyenes előfizetést használja, és frissítenie kell a Standard verzióra a funkció használatához. Ha a FRISSÍTÉST IGÉNYEL szöveg jelenik meg, frissítenie kell az [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) verzióját a funkció használatához. A díjszabással kapcsolatos további információkért olvassa el az Azure Security Center díjszabását ismertető cikket. 
    > 
3. Ha több munkaterületet szeretne vizsgálni, a **SIKERTELEN BEJELENTKEZÉSEK** oszlop szerint rangsorolhatja a vizsgálatot, amely a munkaterületre való sikertelen bejelentkezési kísérletek aktuális számát jeleníti meg. Válassza ki a használni kívánt munkaterületet, és megjelenik az **Identitás és hozzáférés** irányítópult.

    ![identitás és hozzáférés](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Az ezen az irányítópulton rendelkezésre álló információk azonnali segítséget nyújtanak a potenciális gyanús tevékenységek azonosításához. Ez az irányítópult három fő területre oszlik:
    * **Identitás állapota**: összegzi a munkaterületen végzett, identitáshoz kapcsolódó tevékenységeket.
    * **Sikertelen bejelentkezések**: segít gyorsan azonosítani a sikertelen bejelentkezési kísérletek fő okát, és megjeleníti a legtöbb sikertelen bejelentkezést végző tíz fiók listáját.
    * **Bejelentkezések időtúllépése**: segít azonosítani a bejelentkezési időtúllépések mennyiségét, és megjeleníti a legtöbb bejelentkezési kísérletet végző fiókok listáját.
    
A kiválasztott csempétől függetlenül a megjelenő irányítópult a [naplókeresési lekérdezésre](https://docs.microsoft.com/azure/security-center/security-center-search) alapul, az egyetlen különbség a lekérdezés típusa és az eredmény. Egy elemet, például egy számítógépet kiválasztva és rákattintva továbbra is megtekintheti a releváns adatokat. 

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megismerkedett az identitás és a hozzáférés az Azure Security Centerben való figyelésével. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti. 
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.


