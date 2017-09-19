---
title: "Fenyegetések felderítése az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum az Azure Security Center fenyegetésfelderítési funkcióinak használatát mutatja be, amelynek segítségével azonosíthatja a potenciális fenyegetéseket virtuális gépein és számítógépein."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: hu-hu
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Fenyegetések felderítése az Azure Security Centerben
Ez a dokumentum segít az Azure Security Center fenyegetésfelderítés használatában a biztonsággal kapcsolatos problémák kezeléséhez.

## <a name="what-is-threat-intelligence"></a>Mi az a fenyegetésfelderítés?
A Security Centerben elérhető fenyegetésfelderítési szolgáltatással az informatikai rendszergazdák azonosíthatják a környezetre leselkedő biztonsági fenyegetéseket, például ha egy adott számítógép egy botnet része. A számítógépek akkor válhatnak egy botnet csomópontjává, ha a támadók illetéktelenül kártevőket telepítenek a számítógépére, amelyek titokban csatlakoztatják a gépet a vezérlőrendszerhez. A szolgáltatás emellett az illegális kommunikációs csatornákról, például a darknetekről érkező potenciális fenyegetéseket is azonosítani tudja.

A fenyegetésfelderítési képesség kiépítéséhez a Security Center a Microsofton belül több forrásból származó adatokat alkalmaz. A Security Center ezeket az adatokat használva azonosítja a környezetre leselkedő potenciális fenyegetéseket. A Fenyegetésfelderítés panel három fő területet tartalmaz:

- Felderített fenyegetéstípusok
- Fenyegetés forrása
- Fenyegetésfelderítési térkép


## <a name="when-should-i-use-threat-intelligence"></a>Mikor használjam a fenyegetésfelderítést?
A [biztonsági incidensek megoldási folyamatának](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) egyik lépése a rendszer(ek) biztonsági sérülése súlyosságának azonosítása. Ebben a szakaszban a következő feladatokat kell elvégeznie:

- A támadás természetének megállapítása
- A támadás kiindulópontjának meghatározása
- A támadás szándékának azonosítása. A támadás célzottan az Ön szervezete ellen irányult adott információk megszerzése céljából, vagy csak véletlen támadásról van szó?
- A sérült rendszerek azonosítása
- Azonosítsa a támadók által elért fájlokat, és határozza meg azok bizalmasságának mértékét. Ezekben a feladatokban használhatja a Security Center fenyegetésfelderítési adatait. 

## <a name="how-to-access-the-threat-intelligence"></a>Hogyan érthető el a fenyegetésfelderítés?
A környezet aktuális fenyegetésfelderítési állapotának megjelenítéséhez előbb ki kell választania a munkaterületet, ahol az adatok találhatók. Ha nem rendelkezik több munkaterülettel, a munkaterület-választó nem jelenik meg, és a rendszer azonnal a **Fenyegetésfelderítés** irányítópultra lép. A fenyegetésfelderítési irányítópult megnyitásához kövesse az alábbi lépéseket:

1.  Nyissa meg a **Security Center** irányítópultját.
2.  A bal oldali panelen, a **Felderítés** területen kattintson a **Fenyegetésfelderítés** lehetőségre. Megjelenik a **Fenyegetésfelderítés** irányítópult.

    ![Fenyegetések felderítése](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Ha az utolsó oszlopban a **CSOMAG FRISSÍTÉSE** szöveg látható, akkor a munkaterület az ingyenes előfizetést használja, és frissítenie kell a Standard verzióra a funkció használatához. Ha a FRISSÍTÉST IGÉNYEL szöveg jelenik meg, frissítenie kell az [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) verzióját a funkció használatához. A díjszabással kapcsolatos további információkért olvassa el az Azure Security Center díjszabását ismertető cikket. 
    > 
3. Ha több munkaterületet is meg szeretne vizsgálni, a **KÁRTEVŐ IP-CÍMEK** oszlop szerint rangsorolhatja a vizsgálatokat, amelyben a munkaterületen lévő kártevő IP-címek aktuális száma látható. Jelölje ki a használni kívánt munkaterületet, és megjelenik a **Fenyegetésfelderítési** irányítópult.

    ![Fenyegetések felderítése](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Ez az irányítópult négy csempére van osztva:
    * **Fenyegetéstípusok**: a kiválasztott munkaterületen észlelt fenyegetések típusát összegzi.
    * **Származási ország**: a forgalommennyiségeket a forrás helye szerint összesíti.
    * **Fenyegetés helye**: segít azonosítani a különféle helyeket világszerte, ahonnan kommunikáció irányul a környezete felé. A térképen narancsszínű (bejövő) és vörös (kimenő) nyilak láthatóak, amelyek azonosítják a forgalom irányát. Az egyes nyilakra kattintva megtekintheti a fenyegetés típusát és a forgalom irányát.
    * **Fenyegetés részletei**: a térképen kiválasztott fenyegetéssel kapcsolatos további részleteket jeleníti meg.

A megjelenő irányítópult a kiválasztott beállítási csempétől függetlenül mindenképp a [Naplókeresés](https://docs.microsoft.com/azure/security-center/security-center-search) lekérdezésen alapul, az egyetlen különbséget a lekérdezés típusa és az eredmények jelentik.

### <a name="threat-types"></a>Fenyegetéstípusok
A **Fenyegetéstípusok** csempére kattintva megjelenik a **Naplókeresés** irányítópult, amely a bal oldalon a szűrési beállításokat, a jobb oldalon a lekérdezés eredményeit jeleníti meg.

![Keresés a fenyegetésfelderítési adatokban](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

A lekérdezés a fenyegetések összesítéseit név szerint listázza. A bal oldali panel használatával kiválaszthatja az attribútumot, amely alapján szűrni szeretne. Ha például csak az aktuálisan csatlakoztatott gépek fenyegetéseit szeretné megtekinteni, válassza a **Csatlakoztatott** elemet a **SESSIONSTATE** (Munkamenet állapota) szűrőnél, és kattintson az **Alkalmaz** gombra.

![munkamenet állapota](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Azure-beli virtuális gépek esetében csak az ügynökön keresztüláramló hálózati adatok jelennek meg a fenyegetésfelderítési irányítópulton. A fenyegetésfelderítés az alábbi adattípusokat is használja:

- CEF-adatok (Type=CommonSecurityLog)
- WireData (Type= WireData)
- IIS-naplók (Type=W3CIISLog)
- Windows tűzfal (Type=WindowsFirewall)
- DNS-események (Type=DnsEvents)


## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megismerkedett a fenyegetésfelderítés használatával a Security Centerben a gyanús tevékenységek azonosítására. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti. 
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.


