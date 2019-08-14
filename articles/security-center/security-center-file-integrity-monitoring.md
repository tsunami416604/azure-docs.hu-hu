---
title: A fájlok integritásának figyelése Azure Security Centerban | Microsoft Docs
description: " Megtudhatja, hogyan engedélyezheti a fájlok integritásának figyelését a Azure Security Centerban. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: v-mohabe
ms.openlocfilehash: cc0c319357b39ddb3e88d515613273a6f7dc0867
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "65968799"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>A fájlok integritásának figyelése Azure Security Center
Ismerje meg, hogyan konfigurálhatja a fájlok integritásának figyelését (FIM) Azure Security Center az útmutató segítségével.

## <a name="what-is-fim-in-security-center"></a>Mi az a FIM in Security Center?
A fájlok integritásának figyelése (FIM), más néven a változás figyelése, megvizsgálja az operációs rendszer, az alkalmazás szoftvere és mások által a támadásra utaló módosításokat. A rendszer összehasonlító módszert használ annak megállapítására, hogy a fájl aktuális állapota eltér-e a fájl legutóbbi vizsgálatával. Ezt az összehasonlítást kihasználva megállapíthatja, hogy érvényes vagy gyanús módosítások történtek-e a fájlokban.

Security Center a fájl integritásának figyelése ellenőrzi a Windows-fájlok, a Windows-beállításjegyzék és a Linux-fájlok integritását. A FIM engedélyezésével kiválaszthatja a figyelni kívánt fájlokat. Security Center a FIM-mel rendelkező fájlokat figyeli a következő tevékenységekkel:

- Fájl-és beállításjegyzék-létrehozás és-eltávolítás
- Fájl módosításai (a fájlméret változásai, a hozzáférés-vezérlési listán és a tartalom kivonata)
- Beállításjegyzék-módosítások (méret módosítása, hozzáférés-vezérlési listában, típus és tartalom)

Security Center javasolja az entitások figyelését, amelyekkel egyszerűen engedélyezheti a FIM-t. Megadhatja saját FIM-házirendjeit vagy a figyelni kívánt entitásokat is. Ez az útmutató bemutatja, hogyan.

> [!NOTE]
> A Windows és Linux rendszerű számítógépek és virtuális gépek esetében a fájl integritás-figyelése (FIM) szolgáltatás működik, és a standard szintű Security Center érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). A FIM feltölti az adatokat a Log Analytics munkaterületre. Az adatforgalmi díjak a feltöltött adatok mennyiségétől függően érvényesek. További információkért tekintse meg a [log Analytics díjszabását](https://azure.microsoft.com/pricing/details/log-analytics/) .

A FIM az Azure Change Tracking megoldást használja a környezet változásainak nyomon követésére és azonosítására. Ha engedélyezve van a fájl integritásának figyelése, akkor egy **megoldás**típusú **change Tracking** erőforrással rendelkezik. Az adatgyűjtés gyakoriságának részleteiért lásd: [change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) adatgyűjtési adatok az Azure Change tracking.

> [!NOTE]
> Ha eltávolítja a **change Tracking** -erőforrást, a Security Center a fájl sértetlenségének figyelése funkciót is letiltja.

## <a name="which-files-should-i-monitor"></a>Mely fájlokat kell figyelni?
Gondolja át a rendszer és az alkalmazások szempontjából kritikus fontosságú fájlokat a figyelni kívánt fájlok kiválasztásakor. Érdemes lehet olyan fájlokat választania, amelyeket nem várható a tervezés nélkül módosítani. Az alkalmazások vagy az operációs rendszer által gyakran módosított fájlok (például a naplófájlok és szövegfájlok) kiválasztásával nagy mennyiségű zaj hozható létre, ami megnehezíti a támadás azonosítását.

Security Center azt javasolja, hogy a fájl-és beállításjegyzék-módosításokat tartalmazó ismert támadási mintáknak megfelelően milyen fájlokat kell figyelni alapértelmezettként.

## <a name="using-file-integrity-monitoring"></a>A fájlok integritásának figyelése
1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali ablaktábla **speciális felhőalapú védelem**területén válassza a **fájl integritásának figyelése**lehetőséget.
![Security Center irányítópultja][1]

Megnyílik a **fájl integritásának figyelése** .
  ![Security Center irányítópultja][2]

Az egyes munkaterületek esetében az alábbi információk szerepelnek:

- A múlt héten bekövetkezett módosítások teljes száma (egy kötőjelet láthat, ha a FIM nincs engedélyezve a munkaterületen)
- A munkaterületnek jelentett számítógépek és virtuális gépek teljes száma
- A munkaterület földrajzi helye
- Az Azure-előfizetés, amelyre a munkaterület tartozik

A következő gombok is megjelenhetnek egy munkaterülethez:

- ![Engedélyezés ikon][3] Azt jelzi, hogy a FIM nincs engedélyezve a munkaterületen. A munkaterület kiválasztásával engedélyezheti a FIM-t a munkaterület alá tartozó összes gépen.
- ![A Frissítési terv][4] ikon azt jelzi, hogy a munkaterület vagy az előfizetés nem fut a Security Center Standard szint alatt. A FIM szolgáltatás használatához az előfizetésnek standard szintűnek kell lennie.  A munkaterület kiválasztása lehetővé teszi, hogy a standard szintre frissítsen. A standard csomaggal és a frissítéssel kapcsolatos további tudnivalókért tekintse meg a következő témakört: [frissítés a Security Center Standard szintjére a fokozott biztonság](security-center-pricing.md)érdekében.
- Egy üres (nincs gomb) azt jelenti, hogy a FIM már engedélyezve van a munkaterületen.

A **fájl integritásának figyelése**területen kiválaszthat egy munkaterületet, amely engedélyezi a FIM-t az adott munkaterülethez, megtekintheti a munkaterület fájl integritás-figyelési irányítópultját, vagy a munkaterületet a standard [szintre](security-center-pricing.md)

## <a name="enable-fim"></a>FIM engedélyezése
A FIM engedélyezése munkaterületen:

1. A **fájl integritásának figyelése**területen válasszon ki egy munkaterületet az **Engedélyezés** gombbal.
2. A **fájlok integritásának figyelésének engedélyezése** megnyithatja a Windows és a Linux rendszerű gépek számát a munkaterületen.

   ![A fájlok integritásának figyelésének engedélyezése][5]

   A Windows és a Linux ajánlott beállításai is szerepelnek a felsorolásban.  Bontsa ki a **Windows-fájlok**, a **beállításjegyzék**és a **Linux-fájlok** elemet a javasolt elemek teljes listájának megtekintéséhez.

3. Törölje azokat az ajánlott entitásokat, amelyeket nem kíván alkalmazni a FIM használatával.
4. A FIM engedélyezéséhez válassza a **Fájlszűrő-figyelés alkalmazása** lehetőséget.

> [!NOTE]
> Bármikor módosíthatja a beállításokat. További információért lásd: a figyelt entitások szerkesztése.
>
>

## <a name="view-the-fim-dashboard"></a>A FIM-irányítópult megtekintése
A **fájl integritás** -figyelési irányítópultja megjeleníti azokat a munkaterületeket, amelyeken engedélyezve van a FIM. A FIM-irányítópult megnyílik, miután engedélyezte a FIM használatát egy munkaterületen, vagy ha kiválaszt egy munkaterületet a **fájl integritásának figyelése** ablakban, amelyen már engedélyezve van a FIM.

![A fájl integritásának figyelése irányítópult][6]

A munkaterület FIM-irányítópultja a következőket jeleníti meg:

- A munkaterülethez csatlakozó számítógépek teljes száma
- A kiválasztott időszakban történt módosítások teljes száma
- A változás típusának részletezése (fájlok, beállításjegyzék)
- A módosítási kategória részletezése (módosított, hozzáadott, eltávolított)

Az irányítópult tetején található szűrő kiválasztásával azt az időtartamot alkalmazhatja, amelyen a módosításokat látni szeretné.

![Időszak szűrője][7]

A **számítógépek** lap (fent látható) felsorolja a munkaterületnek jelentett összes gépet. Az irányítópultok az egyes gépeknél a következőket sorolja fel:

- A kiválasztott időszakban történt összes módosítás
- A módosítások vagy a beállításjegyzék változásainak teljes változásainak részletezése

A **naplóbeli keresés** akkor nyílik meg, amikor beírja a számítógép nevét a Keresés mezőbe, vagy kiválasztja a számítógépek lapon felsorolt gépet. A naplóbeli keresés megjeleníti a számítógép kiválasztott időszakában végrehajtott összes módosítást. További információért bontsa ki a változást.

![Naplók keresése][8]

A **módosítások** lap (lásd alább) megjeleníti a munkaterület összes változását a kiválasztott időszakban. Minden módosított entitás esetében az irányítópult a következőket sorolja fel:

- A számítógép, amelyen a módosítás történt
- Változás típusa (beállításjegyzék vagy fájl)
- Változás kategóriája (módosított, hozzáadott, eltávolított)
- A módosítás dátuma és időpontja

![A munkaterület módosításai][9]

A Change Details (módosítás **részletei** ) elemre kattintva megadhatja a keresés mezőben megjelenő változást, vagy kijelölhet egy entitást a **módosítások** lapon.

![Változás részletei][10]

## <a name="edit-monitored-entities"></a>Figyelt entitások szerkesztése

1. Térjen vissza a **fájl integritás** -figyelési irányítópultra, és válassza a **Beállítások**lehetőséget.

   ![Beállítások][11]

   A **munkaterület-konfiguráció** három lapot jelenít meg: A **Windows beállításjegyzék**, a **Windows-fájlok**és a Linux- **fájlok**. Minden lap felsorolja az adott kategóriában szerkeszthető entitásokat. Az egyes felsorolt entitások esetében Security Center azonosítja, hogy a FIM engedélyezve van-e (true) vagy nincs engedélyezve (false).  Az entitás szerkesztése lehetővé teszi a FIM engedélyezését vagy letiltását.

   ![Munkaterület-konfiguráció][12]

2. Válasszon ki egy Identity Protectiont. Ebben a példában egy elemet választottunk a Windows beállításjegyzékben. Megnyílik **a Change Tracking szerkesztése** .

   ![Szerkesztés vagy módosítás nyomon követése][13]

**A Change Tracking szerkesztése területen a** következőket teheti:

- A fájl integritás-figyelésének engedélyezése (true) vagy Disable (false)
- Adja meg vagy módosítsa az entitás nevét
- Adja meg vagy módosítsa az értéket vagy az elérési utat
- Törölje az entitást, vesse el a módosítást, vagy mentse a módosítást.

## <a name="add-a-new-entity-to-monitor"></a>Új entitás hozzáadása a figyeléshez
1. Térjen vissza a **fájl integritás** -figyelési irányítópultra, és válassza a felül található **Beállítások** lehetőséget. Megnyílik a **munkaterület konfigurációja** .
2. A **munkaterület konfigurálása**területen válassza ki a hozzáadni kívánt entitás típusának lapfülét: Windows beállításjegyzék, Windows-fájlok vagy Linux-fájlok. Ebben a példában a Linux- **fájlokat**választottuk.

   ![Új elem hozzáadása a figyeléshez][14]

3. Válassza a **Hozzáadás** lehetőséget. **A hozzáadás Change Tracking** megnyílik.

   ![Adja meg a kért adatokat][15]

4. A **Hozzáadás** lapon írja be a kért információkat, majd válassza a **Mentés**lehetőséget.

## <a name="disable-monitored-entities"></a>Figyelt entitások letiltása
1. Térjen vissza a **fájl integritásának figyelése** irányítópultra.
2. Válasszon ki egy munkaterületet, ahol a FIM jelenleg engedélyezve van. Ha nincs megadva az engedélyezés gomb vagy a Frissítési terv gomb, egy munkaterület engedélyezve van a FIM-hez.

   ![Válassza ki azt a munkaterületet, ahol a FIM engedélyezve van][16]

3. A fájl integritásának figyelése területen válassza a **Beállítások**lehetőséget.

   ![Beállítások kiválasztása][17]

4. A **munkaterület konfigurálása**területen válassza ki azt a csoportot, ahol az **engedélyezve** beállítás értéke TRUE (igaz).

   ![Munkaterület konfigurálása][18]

5. A **szerkesztés alatt Change Tracking** az ablak értéke false (hamis) értékre **van** állítva.

   ![Beállítás engedélyezve hamis értékre][19]

6. Kattintson a **Mentés** gombra.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mappa és elérési út figyelése helyettesítő karakterek használatával

Használjon helyettesítő karaktereket a címtárak közötti nyomkövetés egyszerűsítéséhez. A következő szabályok akkor érvényesek, ha a mappák figyelését helyettesítő karakterek használatával konfigurálja:
-   Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
-   Helyettesítő karakterek csak az elérési út utolsó szegmensében használhatók, például C:\folder\file vagy/etc/*. conf
-   Ha egy környezeti változó érvénytelen elérési utat tartalmaz, az érvényesítés sikeres lesz, de az elérési út sikertelen lesz a leltár futtatásakor.
-   Az elérési út beállításakor kerülje el az általános elérési\*utakat, például a c:. * értéket, ami túl sok mappát fog áthaladni.

## <a name="disable-fim"></a>FIM letiltása
A FIM letiltható. A FIM az Azure Change Tracking megoldást használja a környezet változásainak nyomon követésére és azonosítására. A FIM letiltásával eltávolítja a Change Tracking megoldást a kiválasztott munkaterületről.

1. A FIM letiltásához térjen vissza a **fájl integritásának figyelése** irányítópultra.
2. Jelöljön ki egy munkaterületet.
3. A **fájl integritásának figyelése**területen válassza a **Letiltás**lehetőséget.

   ![FIM letiltása][20]

4. A letiltáshoz válassza az **Eltávolítás** lehetőséget.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogy a fájlok integritásának figyelése (FIM) használatával Security Center. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása](tutorial-security-policy.md) – megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
* [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése](security-center-monitoring.md)– megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md)– útmutató a biztonsági riasztások kezeléséhez és megválaszolásához.
* [Partneri megoldások monitorozása](security-center-partner-solutions.md) – megtudhatja, hogyan figyelheti meg a partneri megoldások állapotát.
* [Security Center GYIK](security-center-faq.md)– gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
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
