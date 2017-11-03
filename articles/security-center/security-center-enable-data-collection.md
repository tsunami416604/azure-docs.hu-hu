---
title: "Adatgyűjtés az Azure Security Centerben |} Microsoft Docs"
description: " Megtudhatja, hogyan szeretné az adatgyűjtést az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-in-azure-security-center"></a>Adatgyűjtés az Azure Security Centerben
A Security Center az Azure virtuális gépek (VM) és a nem Azure számítógépek számára figyeli, hogy a biztonsági réseket és a fenyegetések adatait gyűjti. Adatgyűjtés használata a Microsoft Monitoring Agent, különböző biztonsági konfigurációkat és Eseménynapló beolvassa a számítógépről, és másolja az adatokat a munkaterületre elemzés céljából. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. A Microsoft Monitoring Agent összeomlási memóriaképek is másolja a munkaterületre.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>A Microsoft Monitoring Agent-automatikus kiépítés engedélyezése     
Ha automatikus kiépítés engedélyezve van, a Security Center kiosztja a Microsoft Monitoring Agent összes támogatott Azure virtuális gépek és bármely újakat létrehozott. Automatikus kiépítés erősen ajánlott, és az előfizetések a Security Center a Standard szint szükséges.

> [!NOTE]
> Az automatikus létesítési korlátok biztonsági megfigyelés letiltása az erőforrások. További tudnivalókért lásd: [automatikus kiépítés letiltása](security-center-enable-data-collection.md#disable-automatic-provisioning) ebben a cikkben. Virtuálisgép-lemez pillanatfelvételek és összetevő gyűjtemény engedélyezve van, akkor is, ha az Automatikus kiépítés le van tiltva.
>
>

Az a Microsoft Monitoring Agent automatikus kiépítés engedélyezéséhez:
1. Az a Security Center fő menüben válassza a **biztonsági házirend**.
2. Válassza ki az előfizetést.
3. A **biztonsági házirend**, jelölje be **adatgyűjtés**.
4. A **bevezetési**, jelölje be **a** az Automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

![Automatikus kiépítés engedélyezése][1]

## <a name="default-workspace-configuration"></a>Alapértelmezett munkaterület konfigurálása
A Security Center által gyűjtött adatok Naplóelemzési munkaterületek tárolja.  Adatok összegyűjtése az Azure virtuális gépek munkaterületek Security Center által létrehozott vagy egy meglévő munkaterületen létrehozott tárolt választhatja.

A meglévő Naplóelemzési munkaterület használata:
- A munkaterület kijelölt Azure-előfizetése társítva kell lennie.
- Minimális rendelkeznie kell olvasási engedélyeket az eléréséhez a munkaterületen.

Egy meglévő Naplóelemzési munkaterület kiválasztása:

1. A **biztonsági szabályzat – adatgyűjtés**, jelölje be **egy másik munkaterületen**.

   ![Meglévő munkaterület kiválasztása][2]

2. A legördülő menüből válassza ki a munkaterület gyűjtött adatok tárolásához.

> [!NOTE]
> A lekéréses menüre csak munkaterületek férnek hozzá, és az Azure-előfizetéshez vannak rendszerébe.
>
>

3. Kattintson a **Mentés** gombra.
4. Miután kiválasztott **mentése**, kérni fogja, ha szeretné figyelni reconfigure virtuális gépeket.

   - Válassza ki **nem** Ha azt szeretné, hogy az új munkaterület-beállítások csak új virtuális gépekre alkalmazni. Csak az új munkaterület-beállítások alkalmazása ügynöktelepítések; az újonnan felderített virtuális gépek, amelyek nem rendelkeznek a Microsoft Monitoring Agent telepítése.
   - Válassza ki **Igen** Ha azt szeretné, hogy az új munkaterületet beállítások alkalmazni az összes virtuális gépet. Emellett minden virtuális gép csatlakozik egy munkaterület létrehozása a Security Center az új cél munkaterületet csatlakoztatja újra.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, a Security Center által létrehozott mindaddig, amíg az összes virtuális gépet az új cél munkaterülettől újrakapcsolódtak lett munkaterületek nem törölnie kell. A művelet sikertelen lesz, ha a munkaterületet túl korai törlődik.
   >
   >

   - Válassza ki **Mégse** megszakítja a műveletet.

   ![Meglévő munkaterület kiválasztása][3]

## <a name="data-collection-tier"></a>Adatszint gyűjtemény
A Security Center csökkentheti a események mennyiségének elég események vizsgálati, naplózási és fenyegetésészlelés megőrzésével. Kiválaszthatja, hogy az ügynök által gyűjtendő házirend az előfizetések és a munkaterületek származó négy események szűrése a jogosultság.

- **Összes esemény** – az összegyűjtött: Győződjön meg arról, hogy az összes esemény szeretné. Ez az alapértelmezett beállítás.
- **Közös** – Ez olyan események, amely megfelel a legtöbb ügyfél, és lehetővé teszi egy teljes naplózási próbaverzióra.
- **Minimális** – a felhasználóknak, akik minimalizálása érdekében a esemény kötet események körét.
- **Nincs** – tiltsa le a biztonsági események gyűjtése a biztonsági és alkalmazás az AppLocker-naplók. Azok számára, aki ezt a beállítást választania a biztonsági irányítópultok csak a Windows tűzfal naplók és a kártevőirtó, alapkonfiguráció és frissítési például proaktív értékelések rendelkezik.

> [!NOTE]
> Ezeket a kiszolgálócsoportokat tervezték, hogy cím a jellemző forgatókönyvek. Ügyeljen arra, hogy melyik előtt az igényeinek megfelelő kiértékeléséhez.
>
>

Az eseményeket, fog tartozni meghatározásához a **közös** és **minimális** események, ezért előre az ügyfelek és az ipari szabványok további információt a szűretlen gyakran jelennek meg minden esemény és használatát. Ez a folyamat a következő irányelveket is használt:

- **Minimális** -győződjön meg arról, hogy ez csak a sikeres biztonsági szabályok megsértésére utaló események és a fontos eseményeket, amelyek rendelkeznek egy nagyon kis terjedelmű tartalmazza-e. Például a felhasználó sikeres és sikertelen bejelentkezés (esemény azonosítók 4624 4625) tartalmazza, de nem tartalmaz jelentkezzen ki, amely fontos a naplózáshoz, de nem értelmezhető az észlelést, és viszonylag nagy mennyiségű. Ezen adatok mennyisége a legtöbb a bejelentkezési eseményeket és a folyamat létrehozásának esemény (event ID 4688).
- **Közös** -adjon meg egy teljes felhasználói napló ebben a készletben. Ebben a készletben például felhasználói bejelentkezést és a felhasználó kijelentkezése (event ID 4634) tartalmazza. Például a biztonsági csoport módosításait, kulcs tartományvezérlő Kerberos műveleteiről és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózása magában foglalja.

A közös állítja be a fő kifejlesztésének kiválasztása az eseményekre is csökkenthető a, és nem meghatározott események kiszűrésére foglalt eseményeket, amelyek nagyon alacsony.

A biztonsági és alkalmazás tároló eseményazonosítót minden teljes részletes információkat a következő:

   ![Eseményazonosítók][4]

A szűrési házirend kiválasztása:
1. Az a **biztonsági házirend & beállítások** panelen válassza ki a szűrési házirendet a **biztonsági események**.
2. Kattintson a **Mentés** gombra.

   ![Válassza ki a házirend szűrése][5]

## <a name="disable-automatic-provisioning"></a>Automatikus kiépítés letiltása
Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával letilthatja. Automatikus kiépítés erősen ajánlott biztonsági riasztások és javaslatok Rendszerfrissítések, az operációs rendszer biztonsági réseket és az endpoint protection eléréséhez.

> [!NOTE]
> Automatikus kiépítés letiltása nem a Microsoft Monitoring Agent eltávolítása Azure virtuális gépeken, ha az ügynök van kiépítve.
>
>

1. Térjen vissza a Security Center főmenübe, és válassza ki a biztonsági házirendet.

   ![Automatikus kiépítés letiltása][6]

2. Válassza ki az előfizetést, amelyet meg kíván automatikus kiépítés letiltása.
3. A a **biztonsági szabályzat – adatgyűjtés** panelen, a **bevezetési** válasszon **ki** automatikus kiépítés letiltása.
4. Kattintson a **Mentés** gombra.  

## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatta, hogyan gyűjti az adatokat, és a Security Center works automatikus kiépítés. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
- [Az Azure Security Center adatainak biztonsági](security-center-data-security.md) -megtudhatja, hogyan adatok felügyelt és a Security Center védelmét.
* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
