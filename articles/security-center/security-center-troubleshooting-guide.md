---
title: Azure Security Center – Hibaelhárítási útmutató | Microsoft Docs
description: Ez az útmutató informatikai szakembereknek, biztonsági elemzőknek és felhőrendszergazdáknak szól, akiknek az Azure Security Centerrel kapcsolatos problémákelhárítására van szükségük.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 47502e693b897a57517d267924cc6c2752c10440
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585326"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure Security Center – Hibaelhárítási útmutató

Ez az útmutató olyan informatikai (IT) szakemberek, információbiztonsági elemzők és felhőrendszergazdák számára készült, akik szervezetei az Azure Security Centert használják, és el kell hárítaniuk a használathoz kapcsolódó problémákat.

A Security Center a Log Analytics-ügynök segítségével gyűjti és tárolja az adatokat. További információk: [Az Azure Security Center Platform migrálása](security-center-platform-migration.md). A cikkben szereplő információk a Security Center működését jelölik a Log Analytics-ügynökre való áttérés után.

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató a Security Center használatához kapcsolódó problémák hibaelhárítását mutatja be.

Riasztástípusok:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* SQL Database- és SQL Data Warehouse-elemzés
* Környezeti információk

A riasztástípusoktól függően az ügyfelek a riasztás vizsgálatához szükséges információkat az alábbi erőforrások használatával gyűjthetik össze:

* A virtuális gép (VM) eseménymegtekintőjében elérhető biztonsági naplók Windows esetén
* Az AuditD Linux esetén
* Az Azure-tevékenységnaplók és a diagnosztikai naplók engedélyezése a megtámadott erőforráson.

Az ügyfelek visszajelzéseket adhatnak a riasztás leírásával és relevanciájával kapcsolatban. Lépjen a riasztásra, kattintson a **Hasznos volt ez az információ?** gombra, adja meg az okot, és írja be a megjegyzését a visszajelzés indoklására. A riasztások javítása érdekében folyamatosan figyeljük ezeket a visszajelzéseket.

## <a name="audit-log"></a>Napló

A Security Center hibaelhárítása többnyire a meghibásodott összetevőhöz tartozó [auditnapló](../azure-monitor/platform/platform-logs-overview.md) bejegyzéseinek áttekintésével kezdődik. A naplókból a következők állapíthatók meg:

* A végrehajtott műveletek
* A művelet kezdeményezője
* A művelet végrehajtásának időpontja
* A művelet állapota
* A művelet felderítése során hasznosítható egyéb tulajdonságok értékei

A napló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE), nem tartalmazza azonban az olvasási műveleteket (GET).

## <a name="log-analytics-agent"></a>Log Analytics-ügynök

A Security Center a Log Analytics-ügynök – ez ugyanaz az ügynök, amelyet az Azure Monitor szolgáltatás használ – biztonsági adatok gyűjtése az Azure virtuális gépek. Ha az adatgyűjtés engedélyezve van, és az ügynök megfelelően van telepítve a célgépen, elkezdődik az alábbi folyamat végrehajtása:

* HealthService.exe

Ha megnyitja a szolgáltatásfelügyeleti konzolt (services.msc), a Log Analytics-ügynök szolgáltatás az alábbiak szerint fut:

![Szolgáltatások](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Ha meg szeretné tekinteni, hogy az ügynök melyik verziójával rendelkezik, nyissa meg a **Feladatkezelőt**, a **Folyamatok** lapon keresse meg a **Log Analytics ügynöki szolgáltatást,** kattintson rá a jobb gombbal, és kattintson a **Tulajdonságok parancsra.** A **Részletek** lapon keresse meg a fájlverziót az alábbi módon:

![Fájl](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Log Analytics-ügynök telepítési forgatókönyvei

Két telepítési forgatókönyv, amely eltérő eredményeket hozhat, amikor a Log Analytics-ügynök a számítógépre telepítésekor. A támogatott forgatókönyvek:

* **A Security Center automatikusan telepítette az ügynököt**: ebben a forgatókönyvben a Security Centerben és a naplóbeli kereséssel egyaránt megtekintheti a riasztásokat. E-mailes értesítéseket fog kapni arra az e-mail címre, amely az erőforráshoz tartozó előfizetés biztonsági házirendjében konfigurált.

* **Ügynök manuálisan telepítve egy virtuális gép az Azure-ban**található: ebben a forgatókönyvben, ha az ügynökök letöltött és manuálisan telepített február előtt 2017, megtekintheti a riasztásokat a Security Center portálon csak akkor, ha szűri az előfizetés a munkaterület tartozik. Ha azon az előfizetésen szűr, amelyhez az erőforrás tartozik, nem jelennek meg riasztások. E-mailes értesítéseket fog kapni arra az e-mail címre, amely et a munkaterülethez tartozó előfizetés biztonsági szabályzatában konfigurálta.

> [!NOTE]
> A második forgatókönyvben ismertetett viselkedés elkerülése érdekében figyeljen arra, hogy az ügynök legújabb verzióját töltse le.

## <a name="monitoring-agent-health-issues"></a>A Monitoring Agent állapotproblémái <a name="mon-agent"></a>

A **Figyelés állapota** megmutatja, hogy a Security Center miért nem tudja sikeresen monitorozni az automatikus üzembe helyezésre beállított virtuális gépeket és számítógépeket. Az alábbi táblázatban a **Figyelés állapota** értékei, leírásai és a megoldás lépései láthatók.

| Figyelés állapota | Leírás | A megoldás lépései |
|---|---|---|
| Ügynök függőben lévő telepítése | A Log Analytics-ügynök telepítése még mindig fut.  A telepítés akár néhány órát is igénybe vehet. | Várjon, amíg az automatikus telepítés befejeződik. |
| Energiaállapot: ki | A virtuális gép leállt.  A Log Analytics-ügynök csak akkor telepíthető a virtuális gép, amely fut. | Indítsa újra a virtuális gépet. |
| Hiányzó vagy érvénytelen azure-os virtuálisgép-ügynök | A Log Analytics-ügynök még nincs telepítve.  Ahhoz, hogy a Security Center telepítse a bővítményt, érvényes azure-os virtuálisgép-ügynök szükséges. | Telepítse, telepítse újra vagy frissítse a virtuális gépen található azure-os virtuálisgép-ügynököt. |
| A virtuális gép állapota nem áll készen a telepítésre  | A Log Analytics-ügynök még nincs telepítve, mert a virtuális gép nem áll készen a telepítésre. A virtuális gép a virtuálisgép-ügynökkel vagy a virtuális gép üzembe helyezésével kapcsolatos probléma miatt nem áll készen a telepítésre. | Ellenőrizze a virtuális gép állapotát. Térjen vissza a **Virtuális gépek** szakaszra a portálon, és jelölje ki a virtuális gépet az állapotra vonatkozó információk megtekintéséhez. |
|A telepítés nem sikerült – általános hiba | A Log Analytics-ügynök telepítve lett, de hiba miatt nem sikerült. | [Telepítse manuálisan a bővítményt](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) vagy távolítsa el, és a Security Center megpróbálja újból telepíteni. |
| A telepítés nem sikerült – a helyi ügynök már telepítve van | A Log Analytics-ügynök telepítése nem sikerült. A Security Center azonosított egy helyi ügynököt (Log Analytics vagy System Center Operations Manager) már telepítve van a virtuális gépen. A több-helymeghatározó konfiguráció elkerülése érdekében, ahol a virtuális gép két külön munkaterületnek jelent, a Log Analytics ügynök telepítése leállt. | Két megoldás létezik: az egyik [a bővítmény manuális telepítése](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) és csatlakoztatása a kívánt munkaterülethez. A másik a kívánt munkaterület alapértelmezettként való beállítása, és az ügynök automatikus üzembe helyezésének engedélyezése.  Lásd az [automatikus üzembe helyezés engedélyezését](security-center-enable-data-collection.md) ismertető részt. |
| Az ügynök nem tud csatlakozni a munkaterülethez | A Log Analytics-ügynök telepítve van, de a hálózati kapcsolat miatt nem sikerült.  Ellenőrizze az internetkapcsolatot, és hogy érvényes HTTP proxy van-e konfigurálva az ügynökhöz. | Lásd a Monitoring Agent hálózati követelményeit ismertető részt. |
| Az ügynök hiányzó vagy ismeretlen munkaterülethez van csatlakoztatva | Security Center azonosította, hogy a virtuális gépre telepített Log Analytics-ügynök egy olyan munkaterülethez csatlakozik, amelyhez nem fér hozzá. | Ez két esetben fordulhat elő. A munkaterületet törölték, és már nem létezik. Telepítse újra az ügynököt a megfelelő munkaterülettel, vagy távolítsa el az ügynököt, és engedélyezze a Security Centernek az automatikus üzembe helyezési telepítés végrehajtását. A másik eset, amikor a munkaterület egy olyan előfizetés része, amelyhez a Security Center nem rendelkezik engedéllyel. A Security Center működéséhez az előfizetéseknek engedélyezniük kell a hozzáférést a Microsoft Security erőforrás-szolgáltató számára. Az engedélyezéshez regisztrálja az előfizetést a Microsoft Security erőforrás-szolgáltatóban. Ezt megteheti egy API, a PowerShell vagy a portál segítségével, vagy a Security Center **Áttekintés** irányítópultján az előfizetésre történő szűréssel. További információ: [Erőforrás-szolgáltatók és típusaik](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Az ügynök nem válaszol, vagy hiányzik az azonosító | A Security Center annak ellenére sem tudja lekérni a virtuális gépről beolvasott biztonsági adatokat, hogy az ügynök telepítve van. | Az ügynök nem jelent semmilyen adatot, például szívverést sem. Előfordulhat, hogy az ügynök sérült, vagy valami blokkolja a forgalmat. Vagy az ügynök adatokat jelent, de hiányzik egy Azure-erőforrás-azonosító, így lehetetlen az adatok egyeztetése az Azure virtuális gép. A Linux hibaelhárításáról a [Linuxos Log Analytics ügynök hibaelhárítási útmutatója](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal)című témakörben talál. Windows hibaelhárítása: [Windows rendszerű virtuális gépek hibaelhárítása](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Az ügynök nincs telepítve | Az adatgyűjtés le van tiltva. | Kapcsolja be az adatgyűjtést a biztonsági házirendben, vagy telepítse manuálisan a Log Analytics-ügynököt. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>A figyelőügynök hibaelhárítása – hálózati követelmények <a name="mon-network-req"></a>

Ahhoz, hogy az ügynökök kapcsolódni és regisztrálni tudjanak a Security Centerben, hozzáféréssel kell rendelkezniük a hálózati erőforrásokhoz, beleértve a portszámokat és a tartományok URL-címét.

* Proxykiszolgálók esetében biztosítania kell, hogy a megfelelő proxykiszolgáló-erőforrások konfigurálva vannak az ügynök beállításaiban. További információ: [a proxybeállítások módosítása](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
* Ha tűzfallal korlátozza az internet-hozzáférést, akkor a tűzfalat úgy kell beállítania, hogy engedélyezze a Log Analytics hozzáférését. Az ügynök beállításait nem kell módosítania.

Az alábbi táblázat a kommunikációhoz szükséges erőforrásokat tartalmazza.

| Ügynök erőforrása | Portok | HTTPS-ellenőrzés kihagyása |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Igen |
| *.oms.opinsights.azure.com | 443 | Igen |
| *.blob.core.windows.net | 443 | Igen |
| *.azure-automation.net | 443 | Igen |

Ha problémába ütközik az ügynök előkészítése során, olvassa el a következő cikket: [Az Operations Management Suite előkészítési problémáinak hibaelhárítása](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Az Endpoint Protection hibaelhárítása nem működik megfelelően

A vendégügynök a [Microsoft Antimalware](../security/fundamentals/antimalware.md) bővítmény minden műveletének szülőfolyamata. Ha a vendégügynök-folyamat meghibásodik, az annak gyermekfolyamataként futó Microsoft Antimalware is meghibásodhat.  Ilyen esetekben a következők ellenőrzése javasolt:

* A cél virtuális gép egyéni rendszerkép-e, és a virtuális gép létrehozója nem telepítette-e a vendégügynököt.
* Ha a cél nem egy Windows-, hanem egy Linux-alapú virtuális gép, a kártevőírtó bővítmény Windows-verziójának telepítése egy Linux-alapú virtuális gépre sikertelen lesz. A Linux-vendégügynöknek meg kell felelnie az operációs rendszer verziójára és a szükséges csomagokra vonatkozó követelményeknek, és ha ezek a követelmények nem teljesülnek, a virtuálisgép-ügynök sem fog működni.
* A virtuális gépet nem a vendégügynök egy régebbi verziójával hozták-e létre. Ha azzal hozták létre, vegye figyelembe, hogy néhány régebbi ügynök nem frissül automatikusan az újabb verzióra, és ez ehhez a problémához vezethet. Mindig a vendégügynök legfrissebb verzióját használja saját rendszerképek létrehozásához.
* Néhány külső felügyeleti szoftver letilthatja a vendégügynököt, vagy blokkolhatja bizonyos fájlhelyek elérését. Ha külső szoftvert telepített a virtuális gépre, ellenőrizze, hogy az ügynök szerepel-e a kizárási listán.
* Bizonyos tűzfalbeállítások vagy hálózati biztonsági csoportok (NSG) blokkolhatják a vendégügynöktől érkező, illetve az ügynök felé irányuló hálózati forgalmat.
* Bizonyos hozzáférés-vezérlési listák (ACL) megakadályozhatják a lemezhez való hozzáférést.
* Ha nincs elég hely a lemezen, az akadályozhatja a vendégügynök megfelelő működését.

Alapértelmezés szerint a Microsoft Antimalware felhasználói felülete le van tiltva. További információkat az engedélyezéséről [a Microsoft Antimalware felhasználói felületének az Azure Resource Manager-alapú virtuális gépeken üzembe helyezés utáni engedélyezésével](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) kapcsolatos cikkben olvashat.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Az irányítópult betöltési hibáinak elhárítása

Ha problémákat tapasztal a Security Center irányítópultjának betöltése során, bizonyosodjon meg róla, hogy az előfizetést a Security Centerre regisztráló felhasználó (azaz az első felhasználó, aki megnyitotta a Security Centert az előfizetéssel) és az adatgyűjtést bekapcsolni kívánó felhasználó *Tulajdonos* vagy *Közreműködő* az előfizetésen. Ettől a pillanattól az előfizetés *Olvasó* jogú felhasználói is látják az irányítópultot/riasztásokat/ajánlásokat/házirendeket.

## <a name="contacting-microsoft-support"></a>Kapcsolatfelvétel a Microsoft támogatási szolgálatával

Bizonyos problémák a jelen cikk irányelveinek használatával azonosíthatók, a továbbiak leírása pedig a Security Center nyilvános [fórumában](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) található meg. Ha azonban további hibaelhárításra van szüksége, az alábbi módon megnyithat egy új támogatási kérelmet az **Azure Portal használatával:**

![Microsoft támogatási szolgálat](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Lásd még

Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md) – Ismerje meg, hogyan tervezheti meg és ismerheti meg az Azure Security Center bevezetésének tervezési szempontjait.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – További információ a biztonsági riasztások kezeléséről és az azokra való válaszadásról
* [Az Azure Security Center biztonsági riasztásainak megismerése](security-center-alerts-type.md)
* [Oktatóanyag: Reagálás a biztonsági incidensekre](tutorial-security-incident.md)
* [Riasztások érvényesítése az Azure Security Centerben](security-center-alert-validation.md)
* [E-mail-értesítések az Azure Security Centerben](security-center-provide-security-contact-details.md)
* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési képességei](security-center-detection-capabilities.md)
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Ismerje meg, hogyan figyelheti a partnermegoldások állapotát.
* [Az Azure Security Center –](faq-general.md) Gyakori kérdések – Gyakori kérdések a szolgáltatás használatával kapcsolatban
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
