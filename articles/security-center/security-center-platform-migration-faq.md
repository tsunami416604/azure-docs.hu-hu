---
title: A Security Center platform áttelepítési – gyakori kérdések |} Microsoft Docs
description: Ez a GYIK az Azure Security Center platform áttelepítésével kapcsolatos kérdésekre ad választ.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: terrylan
ms.openlocfilehash: 6a88fbadd8fbf05a4942e42b535770f6f068af28
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="security-center-platform-migration-faq"></a>A Security Center platform áttelepítési – gyakori kérdések
Korai. június 2017 az Azure Security Center megkezdte a Microsoft Monitoring Agent használatával gyűjt, és adatokat. További tudnivalókért lásd: [Azure Security Center Platform áttelepítési](security-center-platform-migration.md). Ez a GYIK a platform áttelepítésével kapcsolatos kérdésekre ad választ.

## <a name="data-collection-agents-and-workspaces"></a>Adatok gyűjtése, az ügynökök és a munkaterületek

### <a name="how-is-data-collected"></a>Hogyan gyűjti az adatokat?
A Security Center a Microsoft Monitoring Agent biztonsági adatokat gyűjteni a virtuális gépeket használ. A biztonsági adatok az alábbiakról tájékozódhat:

- biztonsági beállításokkal - használt biztonsági rések azonosítása
- biztonsági események - használt fenyegetések észlelése

Az ügynök által gyűjtött adatok egy meglévő Naplóelemzési munkaterület a virtuális Géphez kapcsolódó vagy a Security Center által létrehozott új munkaterület tárolja. Biztonsági központ létrehoz egy új munkaterületet, ha a virtuális gép a földrajzi hely meghatározásának veszi figyelembe.

> [!NOTE]
> A Microsoft Monitoring Agent a Naplóelemzési szolgáltatás és System Center Operations Manager (SCOM) által használt ugyanannak az ügynöknek.
>
>

Ha engedélyezve van az Automatikus kiépítés (korábbi nevén Naplógyűjtést), vagy az előfizetések áttelepítésekor, a Security Center ellenőrzi, hogy ha a Microsoft Monitoring Agent már telepítve van a virtuális gépek mindegyikének Azure kiterjesztésére. Ha nincs telepítve a Microsoft Monitoring Agent, majd alapértelmezés szerint a Security Center lesz:

- Telepítse a Microsoft Monitoring Agent bővítményt a virtuális Gépen.

   - A Security Center által már létrehozott egy munkaterület szerepel ugyanabban a földrajzi hely, a virtuális géppel, ha az ügynök csatlakozik-e ezen a munkaterületen.
   - Ha a munkaterületet nem létezik, a Security Center hoz létre egy új erőforrás csoport és az alapértelmezett munkaterületi adott földrajzi hely meghatározásának, és csatlakoztatja az ügynököt az adott munkaterület. A munkaterület- és erőforrás csoport elnevezési van:

       Munkaterület: DefaultWorkspace-[előfizetés-azonosító]-[földrajzi]

       Erőforráscsoport: DefaultResouceGroup-[földrajzi]

- Engedélyezze a virtuális gépenként a munkaterületen a Security Center megoldás társított a Security Center tarifacsomagot. Az árakkal kapcsolatos további információkért lásd: [Security Center árképzési](https://azure.microsoft.com/pricing/details/security-center/).
- Az áttelepített csak előfizetések a Security Center is eltávolítja a korábbi Azure Figyelőügynök.

> [!NOTE]
> Bírálja felül a Microsoft Monitoring Agent automatikus telepítését, és a saját munkaterületen.  Lásd: [, hogy az automatikus ügynök telepítése és a munkaterület létrehozása](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) és [használata a meglévő munkaterület](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

A munkaterület helyét a virtuális gép helyét alapul. További tudnivalókért lásd: [adatbiztonság](security-center-data-security.md). Ha az előfizetés virtuális gépek több geolocations tartalmaz, a Security Center több munkaterületek hoz létre. Több munkaterületek fenntartásához adatok adatvédelmi szabályok jönnek létre.

> [!NOTE]
> Platform az áttelepítés előtt a Security Center biztonsági adatokat gyűjtött a virtuális gépek az Azure Figyelőügynök használatával, és a tárolási a tárfiók. A platform az áttelepítést követően a Security Center használatával a Microsoft Monitoring Agent és munkaterület gyűjt, és ugyanazokat az adatokat. A tárfiók távolítható el az áttelepítést követően.  A Security Center is eltávolítja a korábban telepített Azure Monitoring Agent ügynökök platformhoz az áttelepítés után.
>
>

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Vagyok I fizetnie kell a Security Center által létrehozott munkaterületek Naplóelemzési?
Nem. Security Center által létrehozott munkaterületek számlázást, a csomópontonkénti Naplóelemzési beállítása közben nem számítunk Naplóelemzési díjakat. A Security Center számlázási mindig a Security Center biztonsági házirend és a megoldások munkaterület telepítve alapján:

- **Ingyenes szint** – a Security Center lehetővé teszi, hogy a "SecurityCenterFree" megoldást az alapértelmezett munkaterületen. Az ingyenes szint nem kell fizetni.
- **Standard szint** – a Security Center lehetővé teszi, hogy a "Security" megoldást az alapértelmezett munkaterületen.

Az árakkal kapcsolatos további információkért lásd: [Security Center árképzési](https://azure.microsoft.com/pricing/details/security-center/). A tarifákat tartalmazó oldalt megoldást a biztonsági adatok tárolási és arányosított számlázási kezdve. június 2017 módosítását.

> [!NOTE]
> A Security Center által létrehozott munkaterületek tarifacsomag Naplóelemzési nincs hatással a Security Center számlázási.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>A virtuális gépek milyen jogosult a Microsoft Monitoring Agent telepítése automatikus kiépítés?
A Windows vagy Linux IaaS virtuális gépeket csak akkor, ha:

- A Microsoft Monitoring Agent bővítmény jelenleg nincs telepítve a virtuális Gépen.
- A virtuális gép futó állapotban van.
- A Windows vagy Linux rendszerű Virtuálisgép-ügynök telepítve van.
- A virtuális gép nem lesz például webalkalmazási tűzfal vagy új generációs tűzfal készülék.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Törölheti a Security Center által létrehozott alapértelmezett munkaterületek?
**Az alapértelmezett munkaterület törlése nem ajánlott.** A Security Center az alapértelmezett munkaterületek használja a virtuális gépek biztonsági adatok tárolására.  Munkaterület törlése, ha a Security Center nem tudja gyűjteni az adatokat, és egyes biztonsági javaslatok és értesítések nem érhetők el.

Kíván helyreállítani, távolítsa el a virtuális gépeken a törölt munkaterülethez csatlakozik a Microsoft Monitoring Agent. A Security Center a Configuration Manager telepíti az ügynököt, és új alapértelmezett munkaterületek hoz létre.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hogyan használhatja a meglévő Naplóelemzési munkaterületet?

Kiválaszthatja, hogy egy meglévő Naplóelemzési munkaterület Security Center által gyűjtött adatok tárolásához. A meglévő Naplóelemzési munkaterület használata:

- A munkaterület kijelölt Azure-előfizetése társítva kell lennie.
- Minimális rendelkeznie kell olvasási engedélyeket az eléréséhez a munkaterületen.

Egy meglévő Naplóelemzési munkaterület kiválasztása:

1. A **biztonsági szabályzat – adatgyűjtés**, jelölje be **egy másik munkaterületen**.

   ![Másik munkaterület használata][5]

2. A legördülő menüből válassza ki a munkaterület gyűjtött adatok tárolásához.

   > [!NOTE]
   > A lekéréses menüre csak munkaterületek férnek hozzá, és az Azure-előfizetéshez vannak rendszerébe.
   >
   >

3. Kattintson a **Mentés** gombra.
4. Miután kiválasztott **mentése**, kérni fogja, ha szeretné figyelni reconfigure virtuális gépeket.

   - Válassza ki **nem** Ha azt szeretné, hogy az új munkaterületet beállítások **csak új virtuális gépekre alkalmazni**. Csak az új munkaterület-beállítások alkalmazása ügynöktelepítések; az újonnan felderített virtuális gépek, amelyek nem rendelkeznek a Microsoft Monitoring Agent telepítése.
   - Válassza ki **Igen** Ha azt szeretné, hogy az új munkaterületet beállítások **alkalmazni az összes virtuális gép**. Emellett minden virtuális gép csatlakozik egy munkaterület létrehozása a Security Center az új cél munkaterületet csatlakoztatja újra.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, a Security Center által létrehozott mindaddig, amíg az összes virtuális gépet az új cél munkaterülettől újrakapcsolódtak lett munkaterületek nem törölnie kell. A művelet sikertelen lesz, ha a munkaterületet túl korai törlődik.
   >
   >

   - Válassza ki **Mégse** megszakítja a műveletet.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Mi történik, ha a Microsoft Monitoring Agent már telepítve van a virtuális Gépre bővítményként?
A Security Center nem bírálja felül a meglévő kapcsolatok felhasználói munkaterületek. A Security Center biztonsági adatait a virtuális gép már csatlakoztatva van a munkaterületen tárolja. A Security Center tartalmazza a Security Center használatának támogatásához a virtuális gép az Azure erőforrás-Azonosítót a bővítmény verzióra frissíti.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Mi történik, ha korábban a Microsoft Monitoring Agent telepítve a számítógépen, de nem bővítményként?
Ha a Microsoft Monitoring Agent közvetlenül a virtuális gép (nem pedig egy Azure-bővítményt) van telepítve, a Security Center nem telepíti a Microsoft Monitoring Agent és biztonsági figyelés korlátozva.

További információkért tekintse meg a következő szakaszban [mi történik, ha egy SCOM vagy OMS közvetlen ügynök már telepítve van a virtuális hálózat?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>Mi történik, ha egy SCOM vagy OMS közvetlenül az ügynök már telepítve van a virtuális hálózat?
A Security Center nem tudja azonosítani előre, hogy az ügynök telepítve van-e.  A Security Center próbál telepíteni a Microsoft Monitoring Agent bővítményt, és a meglévő telepített ügynök miatt meghiúsul.  Ez a hiba megakadályozza, hogy az ügynök csatlakozási beállítások a munkaterületet, és több homing létrehozása.

> [!NOTE]
> Az ügynök verziószámát frissül a legfrissebb OMS-ügynök verziója.  Ez vonatkozik a SCOM felhasználók számára is.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Mi az, hogy ezek a bővítmények eltávolításának következményei?
Ha eltávolítja a Microsoft Monitoring bővítmény, a Security Center nem állíthatja biztonsági adatokat gyűjteni a virtuális Gépet, és egyes biztonsági javaslatok és értesítések nem érhetők el. 24 órán belül a Security Center, hogy a virtuális Gépet a bővítmény hiányzik, és a bővítmény újratelepíti határozza meg.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hogyan állítsa le az automatikus ügynök telepítési és a munkaterület létrehozását?
Bármikor kikapcsolhatják az előfizetések a biztonsági szabályzatban automatikus kiépítést, de ez nem ajánlott. Kapcsolja ki az automatikus létesítési korlátok Security Center javaslatait és a riasztások. Automatikus kiépítés szükség a Standard tarifacsomag előfizetések. Az Automatikus kiépítés letiltása:

1. Ha az előfizetés a Standard csomag van konfigurálva, nyissa meg az adott előfizetéshez tartozó biztonsági házirendet, és válassza ki a **szabad** réteg.

   ![Tarifacsomag][1]

2. Ezt követően kapcsolja ki a kiválasztásával automatikus kiépítés **ki** a a **biztonsági szabályzat – adatgyűjtés** panelen.
   ![Adatgyűjtés][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Kell I kikapcsolja az automatikus ügynök telepítésére és a munkaterület létrehozását?

> [!NOTE]
> Ne feledje el áttekinteni a szakaszok [Mik azok a engedélyezés következményekkel járhat?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) és [ajánlott lépéseket, ha a engedélyezés](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Ha úgy dönt, hogy automatikus kiépítés megadásának lehetőségét.
>
>

Érdemes lehet tilthatják le az Automatikus kiépítés, ha az Ön számára az alábbiak érvényesek:

- Az automatikus ügynök telepítése a Security Center által az egész előfizetésre vonatkozik.  Automatikus telepítés nem alkalmazható virtuális gépek egy részét. Ha vannak fontos, hogy a Microsoft Monitoring Agent nem telepíthető, majd kell kikapcsolja az Automatikus kiépítés.
- A Microsoft Monitoring Agent-bővítmény telepítése az ügynök verzióját frissíti. Ez vonatkozik a közvetlen ügynök és az SCOM-ügynököt. Ha a telepített SCOM-ügynököt 2012 verzió, és frissítve van, a kezelhetőségi képességeit elveszhet, ha az SCOM-kiszolgálóval is 2012 verzió. Érdemes lehet meggátolható a automatikus kiépítés, ha a telepített SCOM-ügynököt 2012 verzió.
- Ha egy külső az előfizetéshez (egy központosított munkaterületű) egyéni munkaterület akkor kell kikapcsolja az Automatikus kiépítés. Manuálisan telepítse a Microsoft Monitoring Agent bővítményt, és csatlakoztassa a munkaterületen a Security Center nélkül a kapcsolat felülírása.
- Ha el szeretné kerülni előfizetésenként több munkaterületek létrehozását és a saját egyéni munkaterület előfizetésen belül van, majd van két lehetőség közül választhat:

   1. Dönthet úgy is, kívüli automatikus kiépítés. Az áttelepítés után állítsa be az alapértelmezett munkaterület beállításai a [hogyan használhatók a meglévő Naplóelemzési munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Vagy engedélyezheti, hogy az áttelepítés befejezéséhez, a Microsoft Monitoring Agent kell telepíteni a virtuális gépeket, és a virtuális gépek csatlakozni tudjanak a létrehozott munkaterületen. Ezután válassza ki a saját egyéni munkaterület úgy, hogy a már telepített ügynökök újrakonfigurálása kiválasztott alapértelmezett munkaterület setting. További információkért lásd: [hogyan használhatók a meglévő Naplóelemzési munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Mik azok a meggátolható a automatikus kiépítés következményeit?
Az áttelepítés után a Security Center nem állíthatja biztonsági adatokat gyűjteni a virtuális Gépet, és egyes biztonsági javaslatok és értesítések nem érhetők el. Ha kikapcsolja ezt a beállítást, a Microsoft Monitoring Agent manuálisan kell telepítenie. Lásd: [ajánlott lépéseket, ha a engedélyezés](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Mik azok a javasolt lépéseket, ha meggátolható a automatikus kiépítés?
A Microsoft Monitoring Agent manuálisan kell telepítenie, így a Security Center biztonsági adatokat gyűjteni a virtuális gépeket, és adja meg a javaslatokra és riasztásokra. Lásd: [csatlakozás Windows-számítógépek számára az Azure Naplóelemzés szolgáltatás](../log-analytics/log-analytics-windows-agent.md) telepítési útmutatót.

Az ügynök csatlakoztatása a meglévő egyéni munkaterület, vagy a Security Center létre munkaterületet. Ha egy egyéni munkaterület nem rendelkezik a "Security" vagy "SecurityCenterFree" megoldások engedélyezve van, akkor kell alkalmazni a megoldást. Szeretné alkalmazni, válassza ki az egyéni munkaterület vagy előfizetés, és a tarifacsomagot keresztül alkalmazza a **biztonsági szabályzat – tarifacsomag** panelen.

   ![Tarifacsomag][1]

Biztonsági központ lehetővé teszi a kijelölt tarifacsomag alapján munkaterületen a megfelelő megoldást.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hogyan távolítsa el a Security Center által telepített OMS-kiterjesztéseket?
Manuálisan távolítsa el a Microsoft Monitoring Agent. Ez nem ajánlott, mivel a Security Center javaslatait és riasztások korlátozza.

> [!NOTE]
> Adatgyűjtés engedélyezése esetén a Security Center újratelepíti az ügynök az eltávolítása után.  Tiltsa le az adatgyűjtő manuálisan a az ügynök eltávolítása előtt kell. Lásd: [hogyan állítsa le az automatikus ügynök telepítési és a munkaterület létrehozását?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) letiltásáról adatgyűjtés.
>
>

Távolíthatja el kézzel az ügynököt:

1.  Nyissa meg a portál **Naplóelemzési**.
2.  A Naplóelemzési panelen válassza ki a munkaterület:
3.  Válassza ki az egyes virtuális gépek, amelyek nem kívánja figyelni, és válassza ki **Disconnect**.

   ![Az ügynök eltávolítása][3]

> [!NOTE]
> Ha a Linux virtuális gép már nem bővítmény OMS-ügynököt, a bővítmény eltávolítása, valamint az ügynök, és az ügyfél rendelkezik telepíteni kell.
>
>

## <a name="existing-log-analytics-customers"></a>Meglévő Log Analytics-ügyfeleknek

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Bírálja felül a meglévő kapcsolatokat, virtuális gépek és a munkaterületek között a Security Center?
Ha egy virtuális gép már a Microsoft Monitoring Agent telepítve, mint az Azure-bővítményt, a Security Center nem bírálja felül a meglévő munkaterület kapcsolatot. Ehelyett a Security Center használja a meglévő munkaterületen.

A Security Center megoldás a munkaterület van telepítve, ha nem már létezik, és a megoldás csak a megfelelő virtuális gépek lesz alkalmazva. Ha hozzáad egy megoldást, automatikusan telepíti a Naplóelemzési munkaterület csatlakozó Windows és Linux ügynökök alapértelmezés szerint. [Megoldás célcsoportkezelést](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi a hatókör vonatkozik.

Ha a Microsoft Monitoring Agent közvetlenül a virtuális gép (nem pedig egy Azure-bővítményt) van telepítve, a Security Center nem telepíti a Microsoft Monitoring Agent és biztonsági figyelés korlátozva.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Mit tegyek, ha valaki azt gyanítja, hogy az adatáttelepítés platform túllépte a kapcsolatot az egyik virtuális gépek és a saját munkaterület?
Ez nem következik. Ha akkor fordulhat elő, majd [hozzon létre egy Azure támogatási kérést](../azure-supportability/how-to-create-azure-support-request.md) és adja meg a következő adatokat:

- Az Azure erőforrás-azonosító az érintett virtuális gépből
- A munkaterületen, a bővítmény konfigurált előtt megszakadt a kapcsolat az Azure erőforrás-azonosítója
- Az ügynök és a korábban telepített verzió

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Biztonsági központ telepítési megoldások a saját meglévő Naplóelemzési munkaterület? Mik a számlázási megvalósítását?
A Security Center azonosítja, hogy egy virtuális gép már csatlakoztatva van egy munkaterületet hozott létre, amikor a Security Center lehetővé teszi, hogy ezen a munkaterületen a tarifacsomag alapján megoldások. A megoldás csak a megfelelő Azure virtuális gépeken, vonatkoznak keresztül [célcsoport-kezelési megoldás](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), így a számlázási változatlan marad.

- **Ingyenes szint** – a Security Center telepíti a "SecurityCenterFree" megoldás a munkaterületen. Az ingyenes szint nem kell fizetni.
- **Standard szint** – a Security Center telepíti a "Security" megoldás a munkaterületen.

   ![Az alapértelmezett munkaterületi megoldások][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>A környezet már van munkaterületek, használhatók biztonsági adatok gyűjtéséért felelős ügyfélfeladatot?
Ha egy virtuális gép már a Microsoft Monitoring Agent telepítve, mint az Azure-bővítményt, a Security Center használja a meglévő csatlakoztatott munkaterületen. A Security Center megoldás a munkaterület van telepítve, ha nem már létezik, és a megoldás csak a megfelelő virtuális gépek keresztül érvényes [célcsoport-kezelési megoldás](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Ha a Security Center a Microsoft Monitoring Agent telepítése virtuális gépeken, akkor használja a Security Center által létrehozott alapértelmezett munkaterületek. Hamarosan ügyfelek is konfigurálhatja, mely munkaterületek használt.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van a saját munkaterületek biztonsági megoldás. Mik a számlázási megvalósítását?
A biztonsági & naplózási megoldás segítségével engedélyezhető a Security Center Standard szint funkciók Azure virtuális gépekhez. Ha a biztonsági & naplózási megoldás egy munkaterület már telepítve van, a Security Center használja a meglévő megoldást. Nincs változás a számlázási.

## <a name="next-steps"></a>További lépések
A Security Center platform áttelepítésével kapcsolatos további tudnivalókért lásd:

- [Az Azure Security Center Platform áttelepítése](security-center-platform-migration.md)
- [Az Azure Security Center hibaelhárítási útmutatója](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
