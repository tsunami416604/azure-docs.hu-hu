---
title: Fenyegetések felderítése az Azure Security Centerben | Microsoft Docs
description: Ebben a dokumentumban megismerkedhet az Azure Security Center fenyegetésfelderítési funkcióinak használatával, amelyek segítségével azonosíthatja a potenciális fenyegetéseket a virtuális gépein és számítógépein.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: 8f1c6aa2e691a11e8920db8ca8bfdef5b8eb61b9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434190"
---
# <a name="threat-intelligence-in-azure-security-center"></a>Fenyegetések felderítése az Azure Security Centerben
Ez a cikk segít az Azure Security Center fenyegetésfelderítésének a biztonsággal kapcsolatos problémák kezeléséhez történő használatában.

## <a name="what-is-threat-intelligence"></a>Mi az a fenyegetésfelderítés?
A Security Centerben elérhető fenyegetésfelderítési funkcióval az informatikai rendszergazdák azonosíthatják a környezetre leselkedő biztonsági fenyegetéseket. Például azt, ha egy adott számítógép egy botnet része. A számítógépek akkor válhatnak egy botnet csomópontjává, ha a támadók illetéktelenül kártevőket telepítenek a számítógépre, amelyek titokban csatlakoztatják a gépet a vezérlőrendszerhez. A fenyegetésfelderítés emellett az illegális kommunikációs csatornákról, például a dark webről érkező potenciális fenyegetéseket is azonosítani tudja.

A fenyegetésfelderítési képesség biztosításához a Security Center a Microsofton belül több forrásból származó adatokat használ. A Security Center ezeket az adatokat felhasználva azonosítja a környezetre leselkedő potenciális fenyegetéseket. A **Fenyegetésfelderítés** panel három fő területet tartalmaz:

- Felderített fenyegetéstípusok
- Fenyegetés forrása
- Fenyegetésfelderítési térkép


## <a name="when-should-you-use-threat-intelligence"></a>Mikor használjam a fenyegetésfelderítést?
A [biztonsági incidensek megoldási folyamatának](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) egyik lépése a már nem biztonságos rendszer(eke)t ért kár súlyosságának beazonosítása. Ebben a szakaszban a következő feladatokat kell elvégeznie:

- A támadás természetének megállapítása.
- A támadás kiindulópontjának meghatározása.
- A támadás szándékának azonosítása. A támadás célzottan az Ön szervezete ellen irányult, adott információk megszerzése céljából, vagy nem célzott támadásról van szó?
- A sérült rendszerek azonosítása.
- A támadók által elért fájlok azonosítása, és azok bizalmasságának felmérése.

Ezekhez a feladatokhoz használhatja a Security Center fenyegetésfelderítési adatait.

## <a name="access-the-threat-intelligence"></a>A fenyegetésfelderítés elérése
A környezet aktuális fenyegetésfelderítési állapotának megjelenítéséhez előbb ki kell választania azt a munkaterületet, ahol az adatok találhatók. Ha nem rendelkezik több munkaterülettel, a rendszer a munkaterület-választót kihagyva azonnal a **Fenyegetésfelderítés** irányítópultra lép. Az irányítópult elérése:

1. Nyissa meg a **Security Center** irányítópultját.

1. A bal oldali panelen a **veszélyforrások elleni védelem** kiválasztása **Fenyegetésfelderítés**. Megnyílik a térképen.

    ![Fenyegetésfelderítési térkép](./media/security-center-threat-intel/security-center-threat-intel.png)

1. A térkép tetején válassza **megtekintése a klasszikus fenyegetésfelderítés**. A **Fenyegetésfelderítés** irányítópult nyílik meg.

    ![Fenyegetésfelderítési irányítópult](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Ha a jobb szélső oszlopban a **CSOMAG FRISSÍTÉSE** szöveg látható, akkor a munkaterület az ingyenes előfizetést használja. A funkció használatához frissítsen a Standard előfizetésre. Ha a jobb szélső oszlopban a **FRISSÍTÉS SZÜKSÉGES** szöveg látható, frissítse az [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)et a funkció használatához. A díjszabással kapcsolatos további információkért olvassa el az Azure Security Center díjszabását ismertető cikket.
    >
1. Ha több munkaterületet szeretne vizsgálni, rangsorolja a vizsgálatot a **Kártékony IP-címek** oszlop szerint. Ez az oszlop a munkaterülethez tartozó kártékony IP-címek aktuális számát jeleníti meg. Jelölje ki a használni kívánt munkaterületet, és megjelenik a **Fenyegetésfelderítés** irányítópult.

    ![Fenyegetésfelderítési információk](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

1. Az irányítópult négy csempére oszlik:

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
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
