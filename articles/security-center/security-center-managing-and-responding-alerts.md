---
title: Biztonsági riasztások kezelése az Azure Security Centerben | Microsoft Docs
description: Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516106"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben
Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.

> [!NOTE]
> A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. Ennek létezik egy ingyenesen elérhető próbaverziója. A frissítéshez a [Biztonsági szabályzat](tutorial-security-policy.md) beállításnál válassza ki a kívánt tarifacsomagot. További információkért lásd: [Az Azure Security Center díjszabása](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.


> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, olvassa el [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md) című cikket.
>
>

## <a name="managing-security-alerts"></a>Biztonsági riasztások kezelése
A **Biztonsági riasztások** csempén áttekintheti az aktuális riasztásokat. Az egyes riasztásokkal kapcsolatos további részletek megjelenítéséhez kövesse az alábbi lépéseket:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.

    ![Biztonsági riasztások csempe a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. A csempére kattintva nyissa meg a **Biztonsági riasztások** oldalt, amely további információkat tartalmaz a riasztásokról.

   ![A Biztonsági riasztások oldal a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Az oldal alsó részén találhatók az egyes riasztások részletei. Rendezésükhöz kattintson arra az oszlopra, amely szerint rendezni szeretné a riasztásokat. Az oszlopok meghatározása:

* **Leírás**: A riasztás rövid magyarázata.
* **Darabszám**: Az adott típusú, adott napon észlelt riasztások listája.
* **Észlelte**: A riasztást kiváltó szolgáltatás.
* **Dátum**: Az esemény dátuma.
* **Állapot**: Az adott riasztás aktuális állapota. Kétféle állapot létezik:
  * **Aktív**: A rendszer a biztonsági riasztást észlelte.
  * **Elutasította**: A felhasználó elutasította a biztonsági riasztást. Ez az állapot általában olyan riasztások esetén használatos, amelyek ki lettek vizsgálva, vagy amelyek enyhítése vagy megállapítása nem volt tényleges támadásnak.
* **Súlyosság**: A súlyossági szint, amely lehet magas, közepes vagy alacsony.

> [!NOTE]
> A Security Center által létrehozott riasztások az Azure tevékenységnapló területén is megjelennek. További információk az Azure tevékenységnaplójának eléréséről: [A tevékenységnaplók megtekintése az erőforrásokon végzett műveletek naplózásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Riasztás fontossága

-   **Magas**: Nagy valószínűséggel az erőforrás biztonsága sérül. Azonnal megtekintheti. Security Center a kártékony szándék és a riasztás kibocsátására szolgáló megállapítások terén is nagy megbízhatósággal rendelkezik. Például egy olyan riasztás, amely észleli egy ismert kártékony eszköz, például a Mimikatz végrehajtását, a hitelesítő adatok ellopására szolgáló közös eszköz. 
-   **Közepes**: Ez valószínűleg egy gyanús tevékenység, amely arra utalhat, hogy az erőforrást feltörték.
Security Center az elemzésben vagy a keresésben a közepes és a rosszindulatú szándék megbízhatósága közepes és magas. Ezek általában gépi tanulás vagy anomália-alapú észlelések. Például egy rendellenes helyről érkező bejelentkezési kísérlet.
-   **Alacsony**: Ez egy jóindulatú pozitív vagy tiltott támadás lehet. 
    - A Security Center nem eléggé biztos abban, hogy a szándék rosszindulatú, és a tevékenység ártalmatlan lehet. A napló törlése például olyan művelet, amely akkor fordulhat elő, ha egy támadó megpróbálja elrejteni a zeneszámokat, de sok esetben a rendszergazdák által végrehajtott rutin művelet.
    - Security Center általában nem derül ki, ha a támadások blokkolva lettek, kivéve, ha azt javasoljuk, hogy tekintse át a következőt:. 
-   **Tájékoztatás**: A biztonsági incidensek részletezése során csak tájékoztató riasztások jelennek meg, vagy ha a REST API egy adott riasztási AZONOSÍTÓval használja. Az incidensek általában számos riasztásból állnak, amelyek némelyike csak tájékoztató jelleggel jelenhet meg, de a többi riasztás kontextusában érdemes lehet alaposabban megtekinteni.  

> [!NOTE]
> Ha a **2015-06-01-Preview API-** verziót használja, akkor a riasztás súlyossági típusai a fent felsoroltak szerint vannak alkalmazva.  

### <a name="filtering-alerts"></a>A riasztások szűrése
A riasztások dátum, állapot és súlyosság alapján szűrhetők. A riasztások szűrése olyan esetekben lehet hasznos, amikor szűkíteni kell a megjelenített biztonsági riasztások körét. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.

1. Kattintson a **Szűrő** elemre a **Biztonsági riasztások** oldalon. A **Szűrő** megnyitása után válassza ki azokat a dátumra, állapotra és súlyosságra vonatkozó értékeket, amelyeket meg kíván tekinteni.

    ![A riasztások szűrése a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Válaszadás a biztonsági riasztásokra
Válasszon ki egy biztonsági riasztást, ha többet szeretne megtudni a riasztást kiváltó esemény(ek)ről, és arról, hogy milyen lépéseket kell tennie a támadás elhárítása érdekében. A biztonsági riasztások típus és dátum szerint vannak csoportosítva. Ha valamelyik biztonsági riasztásra kattint, megnyílik egy oldal, amely a csoportosított riasztások listáját tartalmazza.

![Válaszadás a biztonsági riasztásokra az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Ebben az esetben a riasztásokat gyanús RDP-tevékenység váltotta ki. Az első oszlopban a támadásnak kitett erőforrások jelennek meg, a másodikban az erőforrást érő támadások száma szerepel, a harmadikban a támadás időpontja látható, a negyedik a riasztás állapotát mutatja, az ötödik pedig a riasztás súlyosságát. Ezen információk áttekintése után kattintson a támadásnak kitett erőforrásra.

![Javaslatok az Azure Security Centerben: mi a teendő a biztonsági riasztással kapcsolatban](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

A **Leírás** mezőben további információkat talál az eseményről. Ezek az információk áttekintést nyújtanak a biztonsági riasztás kiváltó okáról, a célerőforrásról, (adott esetben) a forrás IP-címéről, valamint a fenyegetés elhárítására vonatkozó javaslatokról.  Bizonyos esetekben a forrás IP-címe üres (nem érhető el), mert a Windows nem minden biztonsági eseménynaplója tartalmazza az IP-címet.

A Security Center által javasolt elhárítási műveletek a biztonsági riasztástól függően eltérők lehetnek. Bizonyos esetekben előfordulhat, hogy az Azure egyéb funkcióit kell használnia a javasolt elhárítási művelet végrehajtásához. A támadás szervizelése például nem teszi lehetővé az olyan IP-cím használatát, amely egy [hálózati ACL](../virtual-network/virtual-networks-acl.md) vagy egy [hálózati biztonsági csoport](../virtual-network/security-overview.md#security-rules) szabályával hozza létre ezt a támadást. A különböző típusú riasztásokkal kapcsolatos további információkért olvassa el a [biztonsági riasztások típusairól](security-center-alerts-overview.md#security-alert-types)szóló témakört.

> [!NOTE]
> A Security Centernek korlátozott előzetes verzióként kiadott új észlelései naplózott rekordokat használnak egy közös naplózási keretrendszerben a Linux rendszerű gépek rosszindulatú viselkedésének észlelésére. Amennyiben csatlakozni szeretne az előzetes verzióhoz, küldjön [nekünk](mailto:ASC_linuxdetections@microsoft.com) egy e-mailt, amely tartalmazza az előfizetés-azonosítóit.


## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
