---
title: Fenyegetésészlelési intelligencia és a biztonsági riasztás térkép az Azure Security Centerben |} A Microsoft Docs
description: Ismerje meg, hogyan azonosíthatja a potenciális fenyegetéseket a virtuális gépek és számítógépek használhatja az Azure Security Centerben a biztonsági riasztás térkép és threat intelligence funkció.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: c41cb48e61b4c2bd1dc81e508c8218fad688bbaf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241719"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Biztonsági riasztások térképe és fenyegetések felderítése
Ez a cikk segít az Azure Security Center biztonsági riasztások térkép és biztonsági eseményalapú fenyegetésészlelési térkép biztonsággal kapcsolatos problémák elhárításához.

## <a name="how-the-security-alerts-map-works"></a>Működik, hogy a biztonsági riasztások leképezése
A Security Center biztosít, és a egy térképet, amelynek segítségével azonosíthatja a környezetre leselkedő biztonsági fenyegetéseket. Például azonosíthatja egy adott számítógép egy botnet része-e, és ahol a fenyegetés származik. Számítógépek akkor válhatnak egy botnet csomópontjává, ha a támadók telepítenek kártevők, amelyek titokban kommunikál a parancs és vezérlés, amely a botnet kezelése. 

A térkép létrehozásához, a Security Center a Microsofton belüli különböző forrásokból származó adatokat használ. A Security Center ezeket az adatokat használja a környezetre leselkedő potenciális fenyegetéseket leképezéséhez. 

A [biztonsági incidensek megoldási folyamatának](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) egyik lépése a már nem biztonságos rendszer(eke)t ért kár súlyosságának beazonosítása. Ebben a szakaszban a következő feladatokat kell elvégeznie:

- A támadás természetének megállapítása.
- Az a támadás kiindulópontjának meghatározása.
- A támadás szándékának azonosítása. A támadás célzottan az Ön szervezete ellen irányult, adott információk megszerzése céljából, vagy nem célzott támadásról van szó?
- A sérült rendszerek azonosítása.
- A támadók által elért fájlok azonosítása, és azok bizalmasságának felmérése.

A Security Centerben a biztonsági riasztások térkép segítségével ezek a feladatok ellátását megkönnyítő.

## <a name="access-the-security-alerts-map"></a>Hozzáférés a biztonsági riasztások térkép
Megjelenítheti az adott környezet aktuális észlelt fenyegetésekről, nyissa meg a biztonsági riasztások térkép:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali panelen a **veszélyforrások elleni védelem** kiválasztása **biztonsági riasztások térkép**. Megnyílik a térképen.
3. Részletes tájékoztatást nyújt a riasztást, és javítási lépések fogadására, kattintson a riasztás pontot a térképen, és kövesse az utasításokat. 
 
A biztonsági riasztások térkép riasztások alapján történik. Ezek a riasztások alapuló tevékenységek mely hálózati kommunikáció lett társítva, amely feloldotta a sikeres, az IP-cím egy ismert kockázatos IP-címet (például egy ismert cryptominer)-e IP-cím vagy IP-címet, amely nem ismerhető fel korábban kockázatos. A térkép riasztásokat biztosít bármely Azure-ban korábban kiválasztott előfizetések között. 

A riasztások a térképen a földrajzi helyét, észlelhetők a származó, és súlyosság szerint színkóddal megfelelően jelennek meg. 
    ![Fenyegetésfelderítési információk](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Az esemény-alapú Fenyegetésészlelési irányítópult megtekintése
A nyers biztonsági események alapján fenyegetésfelderítési térkép megtekintéséhez kövesse ezt az eljárást. Ezen a térképen jeleníti meg, hogy csak az IP-cím tekintett kockázati, például egy ismert botnet IP-címet érintő események.

1. Nyissa meg a **Security Center** irányítópultját.

1. A bal oldali panelen a **veszélyforrások elleni védelem** kiválasztása **biztonsági riasztások térkép**. Megnyílik a térképen.
2. Kattintson a jobb felső sarokban **Ugrás a biztonsági események térkép**.
3. Válassza ki a munkaterületet, amelynek meg szeretné tekinteni az irányítópulton.
4. A térkép tetején válassza **megtekintése a klasszikus fenyegetésfelderítés**. A **Fenyegetésfelderítés** irányítópult nyílik meg.

   > [!NOTE]
   > Ha a jobb szélső oszlopban a **CSOMAG FRISSÍTÉSE** szöveg látható, akkor a munkaterület az ingyenes előfizetést használja. A funkció használatához frissítsen a Standard előfizetésre. Ha a jobb szélső oszlopban a **FRISSÍTÉS SZÜKSÉGES** szöveg látható, frissítse az [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)et a funkció használatához. A díjszabással kapcsolatos további információkért olvassa el az Azure Security Center díjszabását ismertető cikket.
   >
5. Ha több munkaterületet szeretne vizsgálni, rangsorolja a vizsgálatot a **Kártékony IP-címek** oszlop szerint. Ez az oszlop a munkaterülethez tartozó kártékony IP-címek aktuális számát jeleníti meg. Jelölje ki a használni kívánt munkaterületet, és megjelenik a **Fenyegetésfelderítés** irányítópult.

    ![Fenyegetésfelderítési információk](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Az irányítópult négy csempére oszlik:

    a.  **Fenyegetéstípusok**. Összegzi a kiválasztott munkaterületen észlelt fenyegetések típusát.

    b.  **Származási ország**. A forgalommennyiségeket a forrás helye szerint összesíti.

    c.  **Fenyegetés helye**. Segít azonosítani az olyan különféle helyeket világszerte, ahonnan kommunikáció irányul a környezete felé. A megjelenített térképen narancssárga (bejövő) és vörös (kimenő) nyilak jelzik a forgalom irányát. Az egyes nyilakra kattintva megtekintheti a fenyegetés típusát és a forgalom irányát.

    d.  **Fenyegetés részletei**. A térképen kiválasztott fenyegetéssel kapcsolatos további részleteket jeleníti meg.

A kiválasztott csempétől függetlenül a megjelenő irányítópult a naplókeresési lekérdezésen alapul. Az egyetlen különbség a lekérdezés típusa és az eredmény.

### <a name="threat-types"></a>Fenyegetéstípusok
Válassza a **Fenyegetéstípusok** csempét a **Naplók keresése** irányítópult megnyitásához. A bal oldalon a szűrőbeállítások, a jobb oldalon pedig a lekérdezési eredmények jelennek meg.

![Naplók keresése](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

A lekérdezés eredménye név szerint jeleníti meg a fenyegetéseket. A bal oldali panel használatával kiválaszthatja azt az attribútumot, amely alapján szűrni szeretne. Ha például csak az aktuálisan csatlakoztatott gépek fenyegetéseit szeretné megtekinteni, válassza a **SESSIONSTATE** (munkamenet állapota) szűrőnél a **Csatlakoztatott** > **Alkalmaz** elemet.

![Munkamenet-állapot](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Azure-beli virtuális gépek esetén csak az ügynökön keresztüláramló hálózati adatok jelennek meg a **Fenyegetésfelderítés** irányítópulton. A fenyegetésfelderítés az alábbi adattípusokat is használja:

- CEF-adatok (Type=CommonSecurityLog)
- WireData (Type= WireData)
- IIS-naplók (Type=W3CIISLog)
- Windows tűzfal (Type=WindowsFirewall)
- DNS-események (Type=DnsEvents)


## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedett a fenyegetésfelderítés a Security Centerben a gyanús tevékenységek azonosítására történő használatával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Az Azure Security Center hibaelhárítási útmutatója](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
