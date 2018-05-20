---
title: Fájl sértetlenségének ellenőrzése az Azure Security Center (előzetes verzió) |} Microsoft Docs
description: " Megtudhatja, hogyan engedélyezze a fájl sértetlenségének ellenőrzése az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: terrylan
ms.openlocfilehash: 722a4fd11f35f04ed22d73638f07d15c49ea3c26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="file-integrity-monitoring-in-azure-security-center-preview"></a>A fájl sértetlenségének ellenőrzése az Azure Security Center (előzetes verzió)
Útmutató a fájl sértetlenségének ellenőrzése (FIM) konfigurálása az Azure Security Centerben használja ezt a forgatókönyvet.

## <a name="what-is-fim-in-security-center"></a>Mi az a FIM a Security Center?
Fájl integritási figyelése (FIM), más néven módosítása a figyelés, fájlok és a módosítások, amelyek jelezhetik a támadás operációs rendszer, alkalmazás és más nyilvántartó megvizsgálja. Összehasonlító metódus segítségével határozza meg, ha a fájl aktuális állapotát eltér a legutóbbi vizsgálat a fájl. Ez az összehasonlítás annak meghatározásához, hogy érvényes vagy gyanús módosításokat lett-e a fájlokat is élvezheti.

A Security Center integritási Fájlfigyelés ellenőrzi az a Windows-fájlok, a Windows beállításjegyzék és a Linux-fájlokat. FIM engedélyezésével figyelt kívánt fájlok kiválasztása A Security Center fájlok figyeli az FIM használatával, például a tevékenység engedélyezve:

- Fájl- és beállításjegyzék-létrehozási és eltávolítása
- Fájlmódosítás (módosítja a fájlméret, hozzáférés-vezérlési listák és a tartalom kivonatát)
- Beállításjegyzék módosításokat (méret, hozzáférés távvezérlő listák, típusa és a tartalom változásait)

A Security Center entitások figyeléséhez, amelyek könnyen engedélyezheti FIM javasolja. Megadhatja a saját FIM házirendek vagy entitás figyelése. Ez a forgatókönyv bemutatja, hogyan.

> [!NOTE]
> A fájl sértetlenségének ellenőrzése (FIM) funkció a Windows és Linux rendszerű számítógépek és a virtuális gépek működik, és érhető el a Security Center Standard csomagra. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
FIM adatok feltöltése a Naplóelemzési munkaterületet. Adatok díjak vonatkoznak, a feltöltött adatok mennyisége alapján. Lásd: [Naplóelemzési árképzési](https://azure.microsoft.com/pricing/details/log-analytics/) további.
>
>

> [!NOTE]
> FIM az Azure változáskövetési megoldás segítségével nyomon követheti és a környezetben bekövetkezett változásokat. Ha a fájl sértetlenségének ellenőrzése engedélyezve van, akkor egy **változások követése** megoldás típusú erőforrást. Ha eltávolítja a **változások követése** erőforrás használata, ha letiltja a hálózatfigyelési szolgáltatást a Security Center fájl integritása.
>
>

## <a name="which-files-should-i-monitor"></a>Mely fájlok kell figyelni?
Meg kell fontolnunk, amíg a fájlokat, amelyek a rendszer és alkalmazások kritikusak, mely fájlok figyelése kiválasztásakor. Vegye figyelembe a tervezés nélkül módosítása nem várt fájlok kiválasztása. Választhatja fájlok, alkalmazások vagy az operációs rendszer (például a naplófájlok és a szövegfájlok) gyakran módosított zaj, ami megnehezíti a azonosíthatjuk a támadást számos létrehozása.

A Security Center javasolja, amely fájlok, célszerű figyelemmel kísérni egy alapértelmezett fájl- és beállításjegyzék-módosításokat tartalmazó támadási mintáknak megfelelően.

## <a name="using-file-integrity-monitoring"></a>Fájl használatával sértetlenségének ellenőrzése
1. Nyissa meg a **Security Center** irányítópultját.
2. Kattintson a bal oldali ablaktábla **speciális felhő védelmi**, jelölje be **integritási Fájlfigyelés**.
![Security Center irányítópultjának][1]

**Integritás figyelési fájl** nyílik meg.
  ![Security Center irányítópultjának][2]

Az alábbi információ az egyes munkaterületeken:

- A múlt héten módosulásainak teljes száma (kötőjellel jelenhetnek meg "-" Ha a munkaterületet nem engedélyezett a FIM)
- Számítógépek és a jelentéskészítés munkaterületét virtuális gépek száma
- A munkaterület földrajzi hely
- Azure-előfizetéssel, amely alatt a munkaterület

A következő gombok munkaterület is jelenhetnek meg:

- ![Ikon engedélyezése][3] Azt jelzi, hogy a FIM nincs engedélyezve a munkaterület. A munkaterület kiválasztása lehetővé teheti, hogy FIM a munkaterületen minden gépen.
- ![Frissítési csomag ikonja][4] annak jelzése, hogy a munkaterület vagy az előfizetés nem fut a Security Center Standard csomagra. A FIM szolgáltatás használatához az előfizetéshez kell futnia a szabványos.  A munkaterület kiválasztása engedélyezi, hogy frissítsen Standard. A Standard szint és frissítésével kapcsolatos további információkért lásd: [, váltson Standard csomagra Security Center az a fokozott biztonság](security-center-pricing.md).
- Egy üres (nincs gomb), az azt jelenti, hogy FIM már engedélyezve van a munkaterületen.

A **fájl sértetlenségének ellenőrzése**, kiválaszthatja, hogy a munkaterület FIM engedélyezése a munkaterületet, és tekintse meg a fájl integritási figyelési irányítópult adott munkaterület vagy [frissítése](security-center-pricing.md) Standard a munkaterületen.

## <a name="enable-fim"></a>FIM engedélyezése
A munkaterület FIM engedélyezése:

1. A **integritási figyelése**, válassza ki a munkaterület a **engedélyezése** gombra.
2. **Fájl integritását a figyelés bekapcsolható** ekkor megnyílik a munkaterületen a Windows és Linux rendszerű gépek számát.

   ![Fájl integritási figyelésének engedélyezése][5]

   A Windows és Linux ajánlott beállítások is szerepelnek.  Bontsa ki a **Windows fájlok**, **beállításjegyzék**, és **Linux fájlok** ajánlott elemek teljes listájának megtekintéséhez.

3. Törölje a jelet a nem kívánja alkalmazni a FIM ajánlott entitások.
4. Válassza ki **alkalmazni a Fájlfigyelés integritási** FIM engedélyezéséhez.

> [!NOTE]
> A beállítások bármikor módosíthatja. Lásd: [Szerkesztés figyelt entitásokat](security-center-file-integrity-monitoring.md#edit-monitored-items) az alábbi további.
>
>

## <a name="view-the-fim-dashboard"></a>A FIM irányítópult megtekintése
A **integritási Fájlfigyelés** irányítópulton megjelenített munkaterületek, amelyben a FIM engedélyezve van. A FIM irányítópult megnyitása a munkaterületen, vagy ha munkaterületeinek választja FIM engedélyezése után a **integritási Fájlfigyelés** ablak, amely már rendelkezik az FIM engedélyezve van.

![Fájl integritási figyelési irányítópult][6]

A FIM irányítópult munkaterület jeleníti meg a következő:

- A munkaterület csatlakozó gépek száma összesen
- A kijelölt időszakban történt változások száma
- Részletes információkat típusának módosítása (fájlokat, beállításjegyzék)
- Változás kategóriája részletes információkat (módosítva lett, hozzáadott, eltávolított)

Az irányítópult tetején a szűrő kiválasztása lehetővé teszi, hogy meg szeretné tekinteni a változásokat a mennyi ideig alkalmazását.

![Period időszűrője][7]

A **számítógépek** (lásd fent) lap felsorolja a jelentéskészítés munkaterületről gépeire. Minden számítógépen az irányítópult sorolja fel:

- A kijelölt időszakban történt módosítások összesen
- Részletes információkat az adatbázisfájl módosításainak vagy a beállításjegyzék változásainak teljes módosításai

**Naplófájl-keresési** megnyílik, ha a számítógép nevét adja meg a Keresés mezőbe, vagy válasszon ki egy, a számítógépek lapján felsorolt gépet. Naplófájl-keresési minden, a gép a kiválasztott időszakban végzett módosításokat jeleníti meg. További információ a módosítás bővítheti.

![Naplók keresése][8]

A **módosítások** (lásd alább) lap felsorolja a munkaterület minden változást a kiválasztott időszakra vonatkozóan. Minden egyes entitásnál megváltozott, az irányítópult listák a:

- A módosítás történt a számítógép
- Olyan változást (beállításjegyzék vagy fájl)
- Változás típusa (módosítva lett, hozzáadott, eltávolított)
- Dátum és idő változás

![A munkaterület változások][9]

**Adatainak módosítása** megnyílik a keresés változás beírásakor mezőbe, vagy egy entitás területén válassza a **módosítások** fülre.

![Adatainak módosítása][10]

## <a name="edit-monitored-entities"></a>Figyelt Szerkesztés entitások

1. Lépjen vissza a **fájl integritási figyelési irányítópult** válassza **beállítások**.

  ![Beállítások][11]

  **Munkaterület konfigurációs** ekkor megnyílik három lappal: **Windows beállításjegyzék**, **Windows fájlok**, és **Linux fájlok**. Minden lap felsorolja a entitások kategória szerkesztése. Minden entitás felsorolt, a Security Center azt jelzi, hogy a FIM van engedélyezve van (igaz), vagy nincs engedélyezve az (false).  Az entitás szerkesztésével segítségével engedélyezheti vagy tilthatja le a FIM.

  ![Munkaterület konfigurálása][12]

2. Válasszon egy identityprotection. Ebben a példában egy elemet a Windows beállításjegyzék azt ki. **A változások követése szerkesztése** nyílik meg.

  ![Szerkesztheti a változáskövetés][13]

A **szerkesztési a változások követése** is:

- (True) engedélyezni vagy letiltani a (hamis) fájl sértetlenségének ellenőrzése
- Adja meg, vagy módosítsa az entitás neve
- Adja meg, vagy módosítsa az értéket vagy elérési útja
- Törölni egy entitást, elveti a módosítást vagy menteni a módosítást

## <a name="add-a-new-entity-to-monitor"></a>Adja hozzá egy új entitás figyelése
1. Lépjen vissza a **figyelési irányítópult fájl integirty** válassza **beállítások** tetején. **Munkaterület konfigurációs** nyílik meg.
2. A **munkaterület konfigurációs**, válassza ki a típusú entitás, aki hozzá szeretne adni a lapot: Windows beállításjegyzék, Windows vagy Linux-fájlokat. Ebben a példában a jelenleg választott **Linux fájlok**.

  ![Figyelendő új elem hozzáadása][14]

3. Válassza a **Hozzáadás** lehetőséget. **Vegye fel a változások követése** nyílik meg.

  ![Adja meg a kért információkat][15]

4. Az a **Hozzáadás** lapon írja be a kért információkat, és válassza **mentése**.

## <a name="disable-monitored-entities"></a>Tiltsa le a figyelt entitások
1. Lépjen vissza a **integritási Fájlfigyelés** irányítópult.
2. Ha jelenleg engedélyezve van a FIM munkaterület kiválasztása. A munkaterület a FIM engedélyezve van, ha hiányzik a engedélyezése vagy terv frissítése gombra.

  ![Ha engedélyezve van-e az FIM munkaterület kiválasztása][16]

3. A fájl integritásának ellenőrzésére, válassza **beállítások**.

  ![Válassza ki a beállítások][17]

4. A **munkaterület konfigurációs**, válasszon ki egy csoportot ahol **engedélyezve** értéke TRUE.

  ![Munkaterület konfigurálása][18]

5. A **szerkesztési a változások követése** ablak set **engedélyezve** False értékre.

  ![Hamis set engedélyezve][19]

6. Kattintson a **Mentés** gombra.

## <a name="disable-fim"></a>FIM letiltása
FIM letilthatja. FIM az Azure változáskövetési megoldás segítségével nyomon követheti és a környezetben bekövetkezett változásokat. Ha letiltja a FIM, a változáskövetési megoldás való eltávolítása.

1. FIM letiltásához adja vissza a **integritási Fájlfigyelés** irányítópult.
2. Jelöljön ki egy munkaterületet.
3. A **integritási Fájlfigyelés**, jelölje be **letiltása**.

  ![FIM letiltása][20]

4. Válassza ki **eltávolítása** letiltása.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, a Security Center fájl sértetlenségének ellenőrzése (FIM) használatára. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása](security-center-policies.md) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés](security-center-monitoring.md)– útmutató az Azure-erőforrások állapotának figyelésére.
* [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md)– útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partnermegoldások figyelése](security-center-partner-solutions.md) – megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Security Center: GYIK](security-center-faq.md)– gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
