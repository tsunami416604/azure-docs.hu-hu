---
title: "Adaptív alkalmazásvezérlők az Azure Security Centerben | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti, hogyan használható az adaptív alkalmazásvezérlés az Azure Security Centerben az Azure-beli virtuális gépeken futó alkalmazások engedélyezési listákra való felvételéhez."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: yurid
ms.openlocfilehash: b1c30b6a0d768f04608c4e46a2e29d20f566964a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Adaptív alkalmazásvezérlők az Azure Security Centerben (előzetes verzió)
Az útmutató azt ismerteti, hogyan konfigurálható az alkalmazásvezérlés az Azure Security Centerben.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Mire szolgálnak a Security Center adaptív alkalmazásvezérlői?
Az adaptív alkalmazásvezérlők segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-beli virtuális gépeken, ami többek között segít felvértezni virtuális gépeit a kártevők ellen. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában. Ez a képesség nagyban leegyszerűsíti az alkalmazások engedélyezési listáinak konfigurálását és karbantartását, és ezáltal lehetővé teszi a következőket:

- Blokkolások vagy riasztások aktiválását a kártevő alkalmazások futtatására tett kísérletek észlelésekor, azokat az eseteket is beleértve, amelyeket a kártevővédelmi megoldások egyébként nem észlelnének.
- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.
- A nem kívánt szoftverek adott környezetben való használatának megelőzését.
- Az elavult és nem támogatott alkalmazások futtatásának letiltását.
- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.
- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

## <a name="how-to-enable-adaptive-application-controls"></a>Az adaptív alkalmazásvezérlők engedélyezése
Az adaptív alkalmazásvezérlők segítségével meghatározhatja a konfigurált erőforráscsoportokon futtatható alkalmazások csoportját. Ez a funkció csak Windows rendszerű (akár klasszikus, akár Azure Resource Manager típusú) gépeken használható. A következő lépésekkel konfigurálhatók az alkalmazások engedélyezési listái a Security Centerben:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali panelen válassza ki az **Advanced cloud defense** (Speciális felhővédelem) szakaszban található **Adaptive application controls** (Adaptív alkalmazásvezérlők) elemet.

    ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Megjelenik az **Adaptív alkalmazásvezérlők** oldal.

![vezérlők](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A **Resource groups** (Erőforráscsoportok) szakaszban három lap található:

* **Configured** (Konfigurált): azon erőforráscsoportok listája, amelyeken már konfigurálva van az alkalmazásvezérlés.
* **Recommended** (Ajánlott): azon erőforráscsoportok listája, amelyek esetében javasolt az alkalmazásvezérlés használata. A Security Center gépi tanulási módszerekkel azonosítja azokat a virtuális gépeket, amelyeken érdemes alkalmazásvezérlést beállítani, az alapján, hogy az adott virtuális gépek folyamatosan ugyanazokat az alkalmazásokat futtatják-e.
* **No recommendation** (Nincs javaslat): azon erőforráscsoportok listája, amelyek olyan virtuális gépeket tartalmaznak, amelyekhez nem tartoznak alkalmazásvezérlési javaslatok, például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

### <a name="configure-a-new-application-control-policy"></a>Új alkalmazásvezérlési szabályzat konfigurálása
1. Kattintson a **Recommended** (Ajánlott) lapra azon erőforráscsoportok listájáért, amelyeken javasolt bevezetni az alkalmazásvezérlést:

  ![Ajánlott](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  A lista a következőket tartalmazza:

  - **NAME** (Név): az előfizetés és az erőforráscsoport neve
  - **VMS** (Virtuális gépek): a virtuális gépek száma az erőforráscsoportban
  - **STATE** (Állapot): a javaslatok állapota, ami az esetek nagy részében az open (nyitott) állapot
  - **SEVERITY** (Súlyosság): a javaslatok súlyossági szintje

2. Jelölje ki az egyik erőforráscsoportot a **Create application control rules** (Alkalmazásvezérlési szabályok létrehozása) lehetőség megnyitásához.

  ![Alkalmazásvezérlési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. A **Select VMs** (Virtuális gépek kiválasztása) felületen tekintse át a javasolt virtuális gépek listáját, és törölje a jelölést azok mellől, amelyeket nem szeretne bevonni az alkalmazásvezérlés hatókörébe. **Az engedélyezési szabályokhoz tartozó folyamatok kiválasztása** felületen tekintse át a javasolt alkalmazások listáját, és törölje a jelölést azok mellől, amelyeket nem szeretne alkalmazni. A lista a következőket tartalmazza:

  - **NAME** (Név): az alkalmazás teljes elérési útja
  - **PROCESSES** (Folyamatok): az egyes elérési utakon található alkalmazások száma
  - **COMMON** (Gyakori): az „Igen” érték azt jelzi, hogy az adott folyamat az erőforráscsoport virtuális gépeinek többségén futtatva volt.
  - **KIHASZNÁLHATÓ**: egy figyelmeztető ikon jelzi, ha az alkalmazás használatával az esetleges támadók megkerülhetik az alkalmazásengedélyezési rendszert. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.
  - **USERS** (Felhasználók): azok a felhasználók, akik engedéllyel rendelkeznek az alkalmazás futtatására

4. Ha végzett a kiválasztással, válassza a **Create** (Létrehozás) lehetőséget.

Alapértelmezés szerint a Security Center minden esetben *vizsgálati* üzemmódban engedélyezi az alkalmazásvezérlést. Miután ellenőrizte, hogy az engedélyezési lista nincs negatív hatással a számítási feladatokra, átválthat *kényszerítési* üzemmódba.

A Security Centernek legalább kétheti adatra van szüksége ahhoz, hogy létrehozhassa az alapkonfigurációt, és feltölthesse a virtuálisgép-csoportok szerinti egyéni javaslatokat. A Security Center standard szintű változatának új ügyfelei számíthatnak arra, hogy a virtuálisgép-csoportjaik először a *nincs javaslat* lapon jelennek meg.

> [!NOTE]
> Ajánlott biztonsági eljárásként a Security Center minden esetben megkísérel létrehozni egy közzétételi szabályt azokra az alkalmazásokra, amelyeket engedélyezni kell, és csak akkor hoz létre elérésiút-szabályt az adott EXE fájl teljes elérési útjához, ha egy adott alkalmazás nem rendelkezik közzétevői adatokkal (azaz nincs aláírva).
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Alkalmazásvezérléssel konfigurált csoportok szerkesztése és monitorozása

1. Az alkalmazásvezérléssel konfigurált csoportok szerkesztéséhez és monitorozásához térjen vissza az **Adaptive application controls** (Adaptív alkalmazásvezérlők) lapra, és a **Resource Groups** (Erőforráscsoportok) területen válassza a **CONFIGURED** (KONFIGURÁLT) elemet:

  ![Erőforráscsoportok](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  A lista a következőket tartalmazza:

  - **NAME** (Név): az előfizetés és az erőforráscsoport neve
  - **VMS** (Virtuális gépek): a virtuális gépek száma az erőforráscsoportban
  - **MODE** (Mód): a vizsgálati üzemmód naplózza az engedélyezési listára fel nem vett alkalmazások indítására tett kísérleteket; a blokkolási mód nem engedélyezi az ilyen alkalmazások futtatását
  - **ISSUES** (Hibák): az aktuális szabálysértések

2. Válasszon ki egy erőforráscsoportot, ha módosításokat szeretne végezni az **Edit application control policy** (Alkalmazásvezérlési szabályzat szerkesztése) oldalon.

  ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. A **Protection Mode** (Védelem módja) alatt a következő lehetőségek közül választhat:

  - **Audit** (Felügyelet): ebben a módban az alkalmazásvezérlési megoldás nem kényszeríti ki a szabályokat, és csak naplózza a tevékenységeket a védett virtuális gépeken. A használata olyan forgatókönyvek esetén javasolt, amikor először szeretné megfigyelni az általános működést, mielőtt blokkolná egy adott alkalmazás futtatását a cél virtuális gépen.
  - **Enforce** (Kényszerítés): ebben a módban az alkalmazásvezérlési megoldás kikényszeríti a szabályokat, és gondoskodik róla, hogy blokkolva legyenek azok az alkalmazások, amelyeknek a futtatása nem engedélyezett.

  Amint azt fent említettük, az új alkalmazásvezérlési szabályzatok alapértelmezés szerint minden esetben *Felügyelet* módban lesznek konfigurálva. A **Policy extension** (Szabályzat kiterjesztése) területen felveheti saját alkalmazásai elérési útját is az engedélyezettek közé. Az elérési utak felvétele után a Security Center ezekhez az alkalmazásokhoz is létrehozza a megfelelő szabályokat a már érvényben lévő szabályok mellett.

  A **Recent Issues** (Legutóbbi hibák) szakasz az aktuális szabálysértéseket sorolja fel.

  ![Hibák](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  A lista a következőket tartalmazza:
  - **ISSUES** (Hibák): a naplózott problémák, amelyek a következők lehetnek:

      - **ViolationsBlocked** (Blokkolt szabálysértések): ha a megoldás kényszerítési módban működik, az engedélyezési listán nem szereplő valamely alkalmazás végrehajtására tett kísérlet.
      - **ViolationsAudited** (Naplózott szabálysértések): ha a megoldás felügyeleti módban működik, az engedélyezési listán nem szereplő valamely alkalmazás végrehajtása.
      - **RulesViolatedManually** (Manuálisan megsértett szabályok): ha egy felhasználó manuálisan, és nem az ASC felügyeleti portálján keresztül próbálja konfigurálni a szabályokat a virtuális gépeken.

 - **OF VMS** (Virtuális gépek száma): azon virtuális gépek száma, amelyeket a hiba jelentkezik.

  Az egyes sorokra kattintva a rendszer átirányítja az [Azure-tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) oldalára, ahol minden olyan virtuális gép adatait megtekintheti, amelyen az adott típusú szabálysértés jelentkezik. Az egyes sorok végén található három pontra kattintva törölheti az adott bejegyzéseket. A **Configured virtual machines** (Konfigurált virtuális gépek) szakasz azokat a virtuális gépeket sorolja fel, amelyekre az adott szabályok vonatkoznak.

  ![Konfigurált virtuális gépek](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  A **Publisher whitelisting rules** (Közzétevői engedélyezési szabályok) alatti lista a következőket tartalmazza:

  - **RULE** (Szabály): olyan alkalmazások, amelyekhez közzétevői szabály lett létrehozva az egyes alkalmazásokhoz tartozó tanúsítványadatok alapján
  - **USERS** (Felhasználók): azon felhasználók száma, akik engedéllyel rendelkeznek az egyes alkalmazások futtatására

  További információkért lásd az [AppLocker közzétevői szabályait ismertető](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) cikket.

  ![Engedélyezési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Az egyes sorok végén található három pontra kattintva törölheti az adott szabályokat, vagy szerkesztheti az engedélyezett felhasználókat.

  A **Path whitelisting rules** (Elérésiút-engedélyezési szabályok) szakasz azon alkalmazások teljes elérési útját tartalmazza (a végrehajtható fájllal együtt), amelyek nincsenek aláírva digitális tanúsítvánnyal, de az engedélyezési szabályokban még szerepelnek.

  > [!NOTE]
  > Alapértelmezés szerint, ajánlott biztonsági eljárásként a Security Center minden esetben megkísérel létrehozni egy közzétételi szabályt az EXE-fájlokra, amelyeket engedélyezni kell, és csak akkor hoz létre elérésiút-szabályt az adott EXE fájl teljes elérési útjához, ha egy adott EXE nem rendelkezik közzétevői adatokkal (azaz nincs aláírva).

  ![Elérésiút-engedélyezési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  A lista a következőket tartalmazza:
  - **NAME**(Név): a végrehajtható fájl teljes elérési útja
  - **USERS** (Felhasználók): azon felhasználók száma, akik engedéllyel rendelkeznek az egyes alkalmazások futtatására

  Az egyes sorok végén található három pontra kattintva törölheti az adott szabályokat, vagy szerkesztheti az engedélyezett felhasználókat.

4. Miután elvégezte a módosításokat az **Adaptive application controls** (Adaptív alkalmazásvezérlők) oldalon, kattintson a **Save** (Mentés) gombra. Ha úgy dönt, hogy nem alkalmazza a módosításokat, kattintson a **Discard** (Elvetés) gombra.

### <a name="not-recommended-list"></a>Nem ajánlott gépek listája

A Security Center csak az olyan virtuális gépeken javasolja az alkalmazásengedélyezés bevezetését, amelyeken az alkalmazások egy stabil készlete fut. Ha a virtuális gépeken az alkalmazások folyamatosan változnak, a rendszer nem hoz létre ajánlásokat.

![Ajánlás](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista a következőket tartalmazza:
- **NAME** (Név): az előfizetés és az erőforráscsoport neve
- **VMS** (Virtuális gépek): a virtuális gépek száma az erőforráscsoportban

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerte, hogyan használhatja az adaptív alkalmazásvezérlőket az Azure Security Centerben az Azure-beli virtuális gépeken futó alkalmazások engedélyezési listákra való felvételéhez. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
