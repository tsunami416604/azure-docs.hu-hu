---
title: Az adatgyűjtést az Azure Security Centerben |} A Microsoft Docs
description: " Ismerje meg, hogyan lehet engedélyezni az adatgyűjtést az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: d70eb1a329b2d1ba560aecbbb4132d2a8e2b7df1
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160127"
---
# <a name="data-collection-in-azure-security-center"></a>Az adatgyűjtést az Azure Security Centerben
A Security Center adatokat gyűjt az Azure-beli virtuális gépek (VM) és a nem Azure-beli számítógépekről a biztonsági rések és fenyegetések monitorozásához. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére. Az ilyen adatok többek között: operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), futó folyamatok, a gép nevét, az IP-címek, bejelentkezett felhasználó, az AppLocker-eseményeket és a bérlő azonosítója. A Microsoft Monitoring Agent az összeomlási memóriaképeket is átmásolja a munkaterülethez.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>A Microsoft Monitoring Agent automatikus kiépítésének engedélyezése     
Alapértelmezés szerint az Automatikus kiépítés le van. Ha az Automatikus kiépítés engedélyezve van, a Security Center létrehozza a Microsoft Monitoring Agentet az összes támogatott Azure-beli és újonnan létrehozott. Az Automatikus kiépítés használata erősen ajánlott, de a manuális ügynöktelepítések is rendelkezésre áll. [Ismerje meg, hogyan telepítse a Microsoft Monitoring Agent bővítményt](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> - Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását. További tudnivalókért lásd: [automatikus kiépítés letiltása](security-center-enable-data-collection.md#disable-automatic-provisioning) ebben a cikkben. Virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése engedélyezve van, még akkor is, ha az Automatikus kiépítés le van tiltva.
> - Adatgyűjtés engedélyezése [adaptív Alkalmazásvezérlők](security-center-adaptive-application.md), konfigurálja a helyi AppLocker-házirend rendszervizsgálati módban, hogy az összes alkalmazást. Ennek hatására az AppLocker események, amelyeket gyűjteni, és a Security Center által használható létrehozása. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 
>

A Microsoft Monitoring Agent automatikus kiépítésének engedélyezése:
1. A Security Center főmenüjében válassza **biztonsági házirend**.
2. Válassza ki az előfizetést.

  ![Előfizetés kiválasztása][7]

3. A **Biztonsági szabályzat** területen válassza az **Adatgyűjtés** elemet.
4. A **automatikus kiépítés**válassza **a** az Automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

  ![Automatikus kiépítés engedélyezése][1]

## <a name="default-workspace-configuration"></a>Alapértelmezett munkaterület-konfiguráció
A Security Center által gyűjtött adatokat Log Analytics-munkaterületen tárolja.  Választhatja, hogy az adatok tárolása a Security Center által létrehozott munkaterületeken vagy egy meglévő munkaterületet hozott létre az Azure virtuális gépekről gyűjtött.

A meglévő Log Analytics-munkaterület használata:
- A munkaterület a kiválasztott Azure-előfizetése társítva kell lennie.
- Minimális rendelkeznie kell olvasási engedéllyel a munkaterület eléréséhez.

Egy meglévő Log Analytics-munkaterület kiválasztása:

1. A **alapértelmezett munkaterület-konfiguráció**válassza **másik munkaterület használata**.

   ![Meglévő munkaterület kiválasztása][2]

2. A legördülő menüből válassza ki a munkaterületet a gyűjtött adatok tárolásához.

  > [!NOTE]
  > A lekéréses menüre, az összes előfizetés között a munkaterületek érhetők el. Lásd: [előfizetés munkaterület kiválasztása adatbázisközi](security-center-enable-data-collection.md#cross-subscription-workspace-selection) további információt.
  >
  >

3. Kattintson a **Mentés** gombra.
4. Miután **mentése**, meg kell adnia, ha szeretné újrakonfigurálja a figyelt virtuális gépek.

   - Válassza ki **nem** Ha azt szeretné, hogy az új munkaterület-beállítások csak új virtuális gépeket a alkalmazni. Az új munkaterület csak vonatkoznak új ügynökök telepítése; az újonnan felderített virtuális gépek, amelyeken nincs telepítve a Microsoft Monitoring Agent telepítve van.
   - Válassza ki **Igen** Ha azt szeretné, hogy az új munkaterület-beállítások minden virtuális gép a alkalmazni. Emellett egy munkaterület létrehozása a Security Center minden virtuális Gépről az új cél munkaterületet újra csatlakoztatni.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem kell törölnie a mindaddig, amíg az összes virtuális gép újra lett csatlakozott az új cél munkaterületet, a Security Center által létrehozott munkaterület(ek). Ez a művelet sikertelen lesz, ha a munkaterület túl korán törlődik.
   >
   >

   - Válassza ki **Mégse** megszakítja a műveletet.

     ![Meglévő munkaterület kiválasztása][3]

## <a name="cross-subscription-workspace-selection"></a>Adatbázisközi előfizetés munkaterület kiválasztása
Amikor kiválaszt egy munkaterületet, az adatok tárolására, az összes előfizetés között a munkaterületek érhetők el. Előfizetések közötti munkaterület kiválasztása lehetővé teszi adatok gyűjtését különböző előfizetésben található virtuális gépeken, és tárolja a munkaterületet az Ön által választott. Ez a funkció a Linux és Windows rendszerű virtuális gépeket is működik.

> [!NOTE]
> Előfizetések közötti a munkaterület kiválasztása az Azure Security Center ingyenes csomag részét képezi. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>
>

## <a name="data-collection-tier"></a>Gyűjtemény adatszint
A Security Center csökkentheti események elegendő a vizsgálati, naplózási és fenyegetésészlelési események fenntartása mellett. Kiválaszthatja a jogot a házirend az előfizetések és munkaterületek négyféle események szűrése gyűjthetők az ügynök által.

- **Az összes esemény** – a vásárlóknak, akik szeretnék, hogy az összes esemény legyenek gyűjtve. Ez az alapértelmezett érték.
- **Közös** – Ez olyan események, amely megfelel a legtöbb ügyfél számára, és lehetővé teszi, hogy teljes auditnaplót.
- **Minimális** – kisebb események egy meghatározott készletének a objem událostí minimalizálása érdekében használni.
- **Nincs** – tiltsa le a biztonsági események gyűjtését a biztonsági és AppLocker-naplók. Azok a vásárlóknak, akik ezt a lehetőséget biztonsági irányítópultokkal csak a Windows tűzfal naplói és a proaktív értékelés például kártevőirtó alapkonfiguráció és frissítési rendelkezik.

> [!NOTE]
> Ezen biztonsági események készletek csak a Security Center Standard szinten érhetők el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
Ezen készletek tervezték, hogy a tipikus forgatókönyvek. Ellenőrizze, hogy melyik illik jobban az igényeinek, még a megvalósítás előtt kiértékeléséhez.
>
>

Meghatározni az eseményeket, fog tartozni a **közös** és **minimális** események, működtünk az ügyfelekkel és az iparági normák megismerheti az egyes események és a használatuk szűretlen gyakoriságát. Ez a folyamat használjuk a következő irányelveket:

- **Minimális** – győződjön meg arról, hogy ez csak olyan eseményeket, amelyek esetleg jelzik a sikeres biztonsági incidenseinek és a fontos eseményekről, amelyek nagyon kevés üzenettel rendelkező vonatkozik-e. Például a felhasználó sikeres és sikertelen bejelentkezés (esemény azonosítók 4624, 4625-ös számú) tartalmazza, de jelentkezzen ki, amely a naplózás fontos, de nem értelmezhető az észlelést, és viszonylag nagy mennyiségű nem tartalmaz. Ezen adatok mennyisége a legtöbb, a bejelentkezési események és folyamat létrehozása event (esemény azonosítója 4688).
- **Közös** – adjon meg egy teljes felhasználói auditnapló ebben a készletben. A készlet például felhasználói bejelentkezéseket és a felhasználói kijelentkezésre (event ID 4634) tartalmazza. Például a biztonsági csoportok változásait, legfontosabb tartomány tartományvezérlő Kerberos műveleti és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózási tartalmazza.

Eseményeket, amelyek nagyon kevés a Rendszeríró a közös állítja be a fő motiváció kiválasztása az összes esemény keresztül csökkentése és a meghatározott események kiszűrésére.

Íme a biztonsági és AppLocker esemény azonosítók az egyes teljes áttekintését:

| Adatszint | Összegyűjtött események mutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Ha a csoportházirend-objektumot (GPO) használ, javasoljuk, hogy engedélyezze-e folyamat létrehozásának esemény 4688 naplórendek és a *CommandLine* mezőt 4688-as belül. Folyamat létrehozása esemény 4688 kapcsolatos további információkért lásd: a Security Center [– gyakori kérdések](security-center-faq.md#what-happens-when-data-collection-is-enabled). További tudnivalókat a naplózási házirendek, lásd: [naplózási szabályzatra vonatkozó javaslatok](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Adatgyűjtés engedélyezése [adaptív Alkalmazásvezérlők](security-center-adaptive-application.md), konfigurálja a helyi AppLocker-házirend rendszervizsgálati módban, hogy az összes alkalmazást. Ennek hatására az AppLocker események, amelyeket gyűjteni, és a Security Center által használható létrehozása. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 
>

A szűrési házirend kiválasztásához:
1. Az a **az adatgyűjtést a biztonsági házirend** panelen jelölje be a szűrési házirend alapján **biztonsági események**.
2. Kattintson a **Mentés** gombra.

   ![Válassza ki a házirend szűrése][5]

## <a name="disable-automatic-provisioning"></a>Automatikus kiépítés letiltása
Letilthatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. Az Automatikus kiépítés erősen ajánlott biztonsági riasztások és javaslatok Rendszerfrissítések, az operációs rendszer biztonsági rések és az endpoint protection információk lekérése érdekében.

> [!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agentet azon Azure-beli virtuális gépekről, ahol az ügynök üzembe lett helyezve.
>
>

1. Térjen vissza a Security Center főmenüjébe, és válassza ki a biztonsági szabályzatot.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az a **biztonsági szabályzat – adatgyűjtés** panel alatt **automatikus kiépítés** kiválasztása **ki**.
4. Kattintson a **Mentés** gombra.

  ![Automatikus kiépítés letiltása][6]

Automatikus kiépítés letiltása (kikapcsolva) esetén az alapértelmezett munkaterület-konfigurációs szakasz nem jelennek meg.

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta, hogyan gyűjti az adatokat, és a Security Center működik az automatikus kiépítést. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
- [Az Azure Security Center által nyújtott adatbiztonság](security-center-data-security.md) – ismerje meg, hogy az adatokat, és hogyan gondoskodik a védelmükről a Security Centerben.
* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
