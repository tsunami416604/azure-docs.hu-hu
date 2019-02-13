---
title: Fájlintegritási monitorozás az Azure Security Centerben |} A Microsoft Docs
description: " Ismerje meg, hogyan engedélyezheti a fájlintegritási Monitorozás az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 50f2c174f21243a70c830c1c30127e0629c81972
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110251"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Fájlintegritási monitorozás az Azure Security Centerben
Ismerje meg a fájl fájlintegritási Monitorozás (FIM) konfigurálása az Azure Security Centerben, használja ezt a forgatókönyvet.

## <a name="what-is-fim-in-security-center"></a>Mit jelent a FIM a Security Centerben?
Fájl fájlintegritási Monitorozás (FIM), más néven módosítása a figyelés, megvizsgálja a fájlokat és az operációs rendszer, alkalmazás és más beállításjegyzékek módosítások, amelyek esetleg jelzik a támadás. Összehasonlító metódussal segítségével határozza meg, ha a fájl aktuális állapotát eltér a legutóbbi vizsgálat a fájl. Ez az összehasonlítás meghatározni, ha a fájlok érvényes vagy gyanús módosításokat végzett használhatja.

A Security Center a fájlintegritási Monitorozás ellenőrzi a Windows-fájlok, a Windows beállításjegyzék és a Linux-fájlok. A FIM engedélyezésével figyelt kívánt fájlok kiválasztása A Security Center figyeli a fájlok a FIM tevékenység például engedélyezve:

- Fájl- és beállításjegyzék létrehozása és eltávolítása
- Fájlmódosítás (módosítja a fájlméret, hozzáférés-vezérlési listák és a tartalom kivonatát)
- Beállításjegyzék-módosításokat (módosítja a mérete, hozzáférés-vezérlési listák, típusa és a tartalom)

Könnyen engedélyezheti a FIM az entitás figyelése, a Security Center javasolja. Megadhatja a saját FIM házirendek vagy entitások figyelése is. Ez az útmutató bemutatja, hogyan.

> [!NOTE]
> A fájl fájlintegritási Monitorozás (FIM) szolgáltatás Windows és Linux rendszerű számítógépek és virtuális gépek esetében működik, és a Security Center Standard szinten érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
FIM feltölti az adatokat a Log Analytics-munkaterületre. Adatok díjak vonatkoznak, a feltöltött adatok mennyisége alapján. Lásd: [Log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/) további.
>
>

> [!NOTE]
> FIM az Azure Change Tracking megoldásával követi és azonosítja a környezet változásainak használ. Ha a fájlintegritási Monitorozás engedélyezve van, akkor egy **Change Tracking** megoldás típusú erőforrás. Ha eltávolítja a **Change Tracking** erőforrás, letiltja a fájlintegritási figyelése a Security Center szolgáltatás.
>
>

## <a name="which-files-should-i-monitor"></a>Mely fájlokat kell figyelése?
Meg kell gondolja át a fájlokat, amelyek kritikus fontosságú a rendszer és alkalmazások, amikor kiválasztja, hogy mely fájlok figyelésére. Vegye figyelembe a tervezés nélkül módosítása nem várt fájlok kiválasztása. Gyakran változnak az alkalmazások vagy az operációs rendszer (például a naplófájlok és a szöveges fájlok) lehetőséget választva fájlok zaj, ami megnehezíti az azonosíthatjuk a támadást rengeteg hozzon létre.

A Security Center javasolja, amely fájlok, célszerű figyelemmel kísérni alapértelmezés szerint a fájl- és beállításjegyzék-módosításokat tartalmazó ismert támadási mintákat.

## <a name="using-file-integrity-monitoring"></a>A fájlintegritási Monitorozás a fájl használatával
1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali ablaktábla **speciális Felhővédelem**válassza **a fájlintegritási Monitorozás**.
![Security Center irányítópultja][1]

**Fájlintegritási monitorozás** nyílik meg.
  ![Security Center irányítópultja][2]

A következő információkat biztosítunk az egyes munkaterületeken:

- A múlt héten bekövetkezett változások teljes száma (kötőjel látni "-" Ha a munkaterület nem engedélyezett a FIM)
- Számítógépek és a-munkaterületre jelentő virtuális gépek teljes száma
- A munkaterület földrajzi helye
- Azure-előfizetést, a munkaterület alatt áll

A következő gombokkal is munkaterülethez tartozó jelenhet meg:

- ![Ikon engedélyezése][3] Azt jelzi, hogy a FIM nincs engedélyezve a munkaterület számára. A munkaterület kiválasztása lehetővé teszi a FIM engedélyezés minden gépen a munkaterületen.
- ![Frissítési csomag ikonja][4] a munkaterületet, vagy az előfizetés nem fut a Security Center Standard szintű jelöli. A FIM szolgáltatás használatához az előfizetés kell futnia a Standard.  A munkaterület kiválasztása lehetővé teszi a Standard. A Standard szintű és frissítésével kapcsolatos további tudnivalókért lásd: [a Security Center Standard csomagra váltásával fokozott biztonságot érhet](security-center-pricing.md).
- Egy üres (nem jelenik meg gomb), az azt jelenti, hogy FIM már engedélyezve van a munkaterülethez.

A **a fájlintegritási Monitorozás**, kiválaszthatja, hogy egy munkaterületet a FIM engedélyezheti az adott munkaterületben, és megtekintheti az adott munkaterülethez tartozó fájlintegritási Monitorozás irányítópultját vagy [frissítése](security-center-pricing.md) a standard munkaterületen.

## <a name="enable-fim"></a>FIM engedélyezése
A munkaterülethez FIM engedélyezése:

1. Alatt **a fájlintegritási Monitorozás**, válasszon ki egy munkaterületet, és a **engedélyezése** gombra.
2. **A fájlintegritási monitorozás engedélyezése** megnyílik a megjelenítés, a Windows és Linux rendszerű gépek munkaterületen számát.

   ![A fájlintegritási monitorozás engedélyezése][5]

   Az ajánlott beállításokat a Windows és Linux rendszereken is megtalálhatók.  Bontsa ki a **Windows fájlok**, **beállításjegyzék**, és **Linux-fájlok** ajánlott elemek teljes listájának megtekintéséhez.

3. Törölje a jelet a javasolt entitások nem FIM a alkalmazni szeretné.
4. Válassza ki **alkalmazni a fájlintegritási monitorozás** FIM engedélyezéséhez.

> [!NOTE]
> A beállításokat bármikor módosíthatja. Lásd: [szerkesztési figyelt entitások](security-center-file-integrity-monitoring.md#edit-monitored-items) az alábbi további.
>
>

## <a name="view-the-fim-dashboard"></a>Az FIM-irányítópultjának megtekintése
A **a fájlintegritási monitorozás** irányítópult munkaterületek, amelyben engedélyezve van a FIM jeleníti meg. A FIM irányítópult megnyílik a munkaterülethez, vagy amikor kiválaszt egy munkaterületet, a FIM engedélyezését követően a **a fájlintegritási Monitorozás** ablak, amely már rendelkezik a FIM engedélyezve van.

![Fájl fájlintegritási Monitorozás irányítópultját][6]

Egy munkaterület a FIM irányítópulton jeleníti meg a következő:

- A munkaterülethez csatlakoztatott gépek teljes száma
- A kijelölt időszak során fellépő változások száma
- Egy bontása típusának módosítása (fájlokat, beállításjegyzék)
- Kategória módosítása egy bontása (módosított, hozzáadott, eltávolított)

Az irányítópult tetején lévő szűrő kiválasztása lehetővé teszi az időtartam, amely meg szeretné tekinteni a módosítása a alkalmazni.

![Időszűrő időszak][7]

A **számítógépek** (lásd fent) lapon látható a jelen munkaterületnek jelentő összes gépen. Az egyes gépek az irányítópult sorolja fel:

- A kijelölt időszakban történt módosítások összesen
- Módosítások összesen, fájl és beállításjegyzék változásainak egy bontása

**Naplóbeli keresés** megnyílik, ha a számítógép nevét adja meg a Keresés mezőbe, vagy válassza ki a számítógépek lapján felsorolt gépet. Naplók keresése a gép a kiválasztott időszakban – kezdeményezett összes módosítás jeleníti meg. További információk változását, bővítheti.

![Naplók keresése][8]

A **módosítások** (lásd alább) lapon látható a munkaterület összes módosítást a kijelölt időszakban. Az egyes entitásokhoz módosított irányítópult listák a:

- A módosítás történt a számítógép
- Egy olyan változást (beállításjegyzék vagy fájl)
- Változás típusa (módosított, hozzáadott, eltávolított)
- Dátum és idő módosítása

![A munkaterületre vonatkozó módosítások][9]

**Változás részletei** megnyílik, amikor változás adja meg a Keresés mezőben, vagy válasszon ki egy entitáshoz tartozó a **módosítások** fülre.

![Változás részletei][10]

## <a name="edit-monitored-entities"></a>Figyelt szerkesztési entitások

1. Lépjen vissza a **fájlintegritási Monitorozás irányítópultját** válassza **beállítások**.

  ![Beállítások][11]

  **Munkaterület-konfiguráció** megnyílik a Megjelenítés a három lappal: **Windows beállításjegyzék**, **Windows fájlok**, és **Linux-fájlok**. Minden lap, Ön által szerkeszthető, amelynek-entitásokat sorolja fel. Minden entitás szerepel, a Security Center azonosítja a FIM-e (igaz) engedélyezve van, vagy nincs engedélyezve az (hamis).  Az entitás szerkesztését teszi lehetővé engedélyezheti vagy tilthatja le a FIM.

  ![Munkaterület konfigurálása][12]

2. Válasszon egy identityprotection. Ebben a példában egy elem, a Windows beállításjegyzék kiválasztott. **A Change Tracking megoldásba szerkesztése** nyílik meg.

  ![Szerkesztheti, vagy a változáskövetés][13]

A **szerkesztése a Change Tracking megoldásba** is:

- (True) engedélyezése vagy letiltása a (hamis) a fájlintegritási monitorozás
- Adja meg, vagy módosítani az entitás neve
- Adja meg vagy módosítsa az értéket vagy az elérési út
- Törölni az entitást, elveti a módosítást, vagy a módosítás mentéséhez

## <a name="add-a-new-entity-to-monitor"></a>Adjon hozzá egy új entitás figyelése
1. Lépjen vissza a **fájlintegritási monitorozás irányítópult** válassza **beállítások** tetején. **Munkaterület-konfiguráció** nyílik meg.
2. A **munkaterület-konfiguráció**, válassza ki a lapon, hogy a hozzáadni kívánt entitás típusa: Windows beállításjegyzék, a Windows-fájlokat vagy a Linux-fájlok. Ebben a példában a kiválasztott **Linux-fájlok**.

  ![Adjon hozzá egy új elem figyelése][14]

3. Válassza a **Hozzáadás** lehetőséget. **Adja hozzá a Change Tracking megoldásba** nyílik meg.

  ![Adja meg a kért adatokat][15]

4. Az a **Hozzáadás** lapon írja be a kért adatokat, és válassza **mentése**.

## <a name="disable-monitored-entities"></a>Tiltsa le a felügyelt entitások
1. Lépjen vissza a **a fájlintegritási Monitorozás** irányítópultot.
2. Válasszon egy munkaterületet, ahol jelenleg engedélyezve van a FIM. A munkaterület a FIM engedélyezve van, ha hiányzik az Engedélyezés gombra vagy a csomag frissítése gombra.

  ![Válasszon ki egy munkaterületet, ahol engedélyezve van-e az FIM][16]

3. A fájlintegritási Monitorozás, válassza a **beállítások**.

  ![beállítások kiválasztása][17]

4. Alatt **munkaterület-konfiguráció**, válasszon ki egy csoportot, **engedélyezve** értéke igaz.

  ![Munkaterület konfigurálása][18]

5. A **szerkesztése a Change Tracking megoldásba** ablak set **engedélyezve** hamis értékre.

  ![Hamis értékre beállított engedélyezve][19]

6. Kattintson a **Mentés** gombra.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mappa- és elérési út figyelési helyettesítő karakterek használatával

A helyettesítő karakterek használatával egyszerűsíthető a nyomon követési címtárak között. Az alábbi szabályok vonatkoznak a mappa figyelése helyettesítő konfigurálásakor:
-   A helyettesítő karakterek is szükséges a több fájlok nyomon követése.
-   Helyettesítő karaktereket csak a elérési útját, például C:\folder\file vagy /etc/*.conf utolsó szegmense használható
-   Ha egy környezeti változó tartalmaz egy elérési utat, amely nem érvényes, érvényesítés sikeres lesz, de az elérési út sikertelen lesz, ha szoftverleltár fut le.
-   Az elérési út megadásakor elkerülése érdekében az általános elérési utak, például: c:\*. * áthaladhat túl sok mappák eredményez.

## <a name="disable-fim"></a>FIM letiltása
FIM letilthatja. FIM az Azure Change Tracking megoldásával követi és azonosítja a környezet változásainak használ. FIM letiltásával eltávolítja a Change Tracking megoldás kijelölt munkaterületen.

1. FIM letiltásához adja vissza a **a fájlintegritási Monitorozás** irányítópultot.
2. Jelöljön ki egy munkaterületet.
3. A **a fájlintegritási Monitorozás**válassza **letiltása**.

  ![FIM letiltása][20]

4. Válassza ki **eltávolítása** letiltása.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan fájl fájlintegritási Monitorozás (FIM) a Security Center használatával. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása](tutorial-security-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés](security-center-monitoring.md)– útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md)– ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása](security-center-partner-solutions.md) – útmutató a partnermegoldások állapotának monitorozásához.
* [A Security Center – gyakori kérdések](security-center-faq.md)– gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

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
