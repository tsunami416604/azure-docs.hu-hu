---
title: Fájlintegritás figyelése az Azure Security Centerben | Microsoft dokumentumok
description: Ebből a forgatókönyvből megtudhatja, hogyan konfigurálhatja a fájlintegritás-figyelést (FIM) az Azure Security Centerben.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 4d65ca8d97e1cca81886259d4f15cc880e45be9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604289"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Fájlintegritás monitorozása az Azure Security Centerben
Ebből a forgatókönyvből megtudhatja, hogyan konfigurálhatja a fájlintegritás-figyelést (FIM) az Azure Security Centerben.

## <a name="what-is-fim-in-security-center"></a>Mi az a FIM a Security Centerben?
A fájlintegritás-figyelés (FIM), más néven a változásfigyelés, megvizsgálja az operációs rendszer, az alkalmazásszoftverek és mások fájljait és nyilvántartásait, hogy nem történt-e támadás. Összehasonlítási módszerrel állapítható meg, hogy a fájl aktuális állapota eltér-e a fájl utolsó vizsgálatától. Ezzel az összehasonlítással megállapíthatja, hogy érvényes vagy gyanús módosítások történtek-e a fájlokon.

A Security Center fájlintegritás-figyelése ellenőrzi a Windows-fájlok, a Windows rendszerleíró adatbázis és a Linux-fájlok integritását. A figyelni kívánt fájlokat a FIM engedélyezésével választhatja ki. A Security Center olyan fájlokat figyel, amelyekfim-funkciója engedélyezve van a következő tevékenységekhez:

- Fájl- és rendszerleíró adatbázis létrehozása és eltávolítása
- Fájlmódosítások (a fájlméret módosítása, hozzáférés-vezérlési listák és a tartalom kivonata)
- A rendszerleíró adatbázis módosításai (méretváltozások, hozzáférés-vezérlési listák, típus és tartalom)

A Security Center azt javasolja, hogy az entitások figyeljék a figyelést, amelyeken egyszerűen engedélyezheti a FIM-et. Saját FIM-szabályzatokat vagy -entitásokat is definiálhat. Ez a forgatókönyv bemutatja, hogyan.

> [!NOTE]
> A Fájlintegritás-figyelés (FIM) szolgáltatás Windows és Linux rendszerű számítógépeken és virtuális gépeken működik, és a Security Center standard szintjén érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). A FIM adatokat tölt fel a Log Analytics-munkaterületre. A feltöltött adatok mennyisége alapján adatforgalmi díjak merülnek fel. További információ: [Log Analytics-díjszabás.](https://azure.microsoft.com/pricing/details/log-analytics/)

A FIM az Azure Change Tracking megoldás segítségével nyomon követheti és azonosíthatja a környezetváltozásait. Ha a fájlintegritás figyelése engedélyezve van, **megoldás**típusú **változáskövetési** erőforrással rendelkezik. Az adatgyűjtés gyakoriságának részleteiről az Azure Change Tracking változáskövetési adatai a [Változáskövetés szolgáltatással kapcsolatos részletekről.](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details)

> [!NOTE]
> Ha eltávolítja a **változáskövetés** erőforrást, a Security Center fájlintegritás-figyelési szolgáltatását is letiltja.

## <a name="which-files-should-i-monitor"></a>Mely fájlokat kell figyelni?
A rendszer és az alkalmazások szempontjából kritikus fájlokra kell gondolnia, amikor kiválasztja, hogy mely fájlokat szeretné figyelni. Fontolja meg olyan fájlok kiválasztását, amelyek tervezés nélkül várhatóan nem változnak. Az alkalmazások vagy az operációs rendszer által gyakran módosított fájlok (például naplófájlok és szövegfájlok) kiválasztása sok zajt okoz, ami megnehezíti a támadás azonosítását.

A Security Center azt javasolja, hogy mely fájlokat kell alapértelmezettként figyelni a fájl- és beállításjegyzék-módosításokat tartalmazó ismert támadási minták nak megfelelően.

## <a name="using-file-integrity-monitoring"></a>A fájlintegritás figyelésének használata
1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali ablaktáblában, az **Advanced Cloud Defense**területen válassza a **Fájlintegritás figyelése**lehetőséget.
![A Security Center irányítópultja][1]

**Megnyílik a fájlintegritás figyelése.**
  ![A Security Center irányítópultja][2]

Az egyes munkaterületek esetében a következő információk állnak rendelkezésre:

- Az elmúlt héten bekövetkezett módosítások teljes száma (a "-" kötőjel jelenhet meg, ha a FIM nincs engedélyezve a munkaterületen)
- A munkaterületre jelentést tevő számítógépek és virtuális gépek teljes száma
- A munkaterület földrajzi helye
- Olyan Azure-előfizetés, amely alatt a munkaterület található

A következő gombok is megjelenhetnek egy munkaterülethez:

- ![Engedélyezés ikon][3] Azt jelzi, hogy a FIM nincs engedélyezve a munkaterülethez. A munkaterület kiválasztásával engedélyezheti a FIM-et a munkaterület alatti összes gépen.
- ![Frissítési][4] terv ikon Azt jelzi, hogy a munkaterület vagy az előfizetés nem fut a Security Center standard szintje alatt. A FIM funkció használatához az előfizetésnek a Standard szolgáltatást kell futtatnia.  A munkaterület kiválasztásával standard ra frissíthet. Ha többet szeretne megtudni a standard szintről és a frissítésről, olvassa [el a Frissítés a Security Center standard szintjére a fokozott biztonság érdekében című témakört.](security-center-pricing.md)
- Az üres (nincs gomb) azt jelenti, hogy a FIM már engedélyezve van a munkaterületen.

A **Fájlintegritás figyelése**területen kiválaszthat egy munkaterületet, amely engedélyezi a FIM-et az adott munkaterülethez, megtekintheti az adott munkaterület Fájlintegritás-figyelési irányítópultját, vagy [frissítheti](security-center-pricing.md) a munkaterületet Standard ra.

## <a name="enable-fim"></a>FIM engedélyezése
A FIM engedélyezése a munkaterületen:

1. A **Fájlintegritás figyelése**csoportban válasszon ki egy munkaterületet az **Engedélyezés** gombbal.
2. **A fájlintegritás figyelésének engedélyezése** megnyílik, és megnyílik a munkaterület alatt található Windows- és Linux-gépek száma.

   ![Fájlintegritás-figyelés engedélyezése][5]

   A Windows és linux ajánlott beállításai is fel vannak sorolva.  Bontsa ki a **Windows-fájlokat**, **a rendszerleíró adatbázist**és a **Linux-fájlokat** az ajánlott elemek teljes listájának megtekintéséhez.

3. Törölje a jelet az on-fim-t nem alkalmazni kívánt ajánlott entitások jelölőnégyzetből.
4. Válassza **a Fájlintegritás figyelésének alkalmazása** a FIM engedélyezéséhez lehetőséget.

> [!NOTE]
> A beállításokat bármikor módosíthatja. További információért olvassa el az alábbi Figyelt entitások szerkesztése.
>
>

## <a name="view-the-fim-dashboard"></a>A FIM irányítópultjának megtekintése
A **Fájlintegritás figyelési** irányítópultja olyan munkaterületeknél jelenik meg, ahol a FIM engedélyezve van. A FIM-irányítópult akkor nyílik meg, ha engedélyezte a FIM-et egy munkaterületen, vagy ha kiválaszt egy munkaterületet a **Fájlintegritás figyelése** ablakban, amelyen már engedélyezve van a FIM.

![Fájlintegritás-figyelési irányítópult][6]

A munkaterület FIM irányítópultja a következő részleteket jeleníti meg:

- A munkaterülethez csatlakoztatott gépek száma összesen
- A kiválasztott időszakban bekövetkezett változások teljes száma
- A változás típusának lebontása (fájlok, rendszerleíró adatbázis)
- A változáskategória bontása (módosítva, hozzáadva, eltávolítva)

Ha az irányítópult tetején a Szűrő lehetőséget választja, alkalmazhatja azt az időtartamot, amelyhez a módosításokat látni szeretné.

![Időszakszűrő][7]

A **Számítógépek** lap (lásd fent) felsorolja az összes gépet, amely erre a munkaterületre jelentést tesz. Az irányítópult minden egyes géphez a következőket sorolja fel:

- A kiválasztott időszakban bekövetkezett összes változás
- Az összes módosítás lebontása a fájlváltozások vagy a rendszerleíró adatbázis módosításakor

**A naplókeresés** akkor nyílik meg, amikor beír egy számítógépnevet a keresőmezőbe, vagy kijelöl egy számítógépet a Számítógépek lapon. További információért kibonthatja a módosítást.

![Naplók keresése][8]

A **Változások** lap (lásd alább) felsorolja a munkaterület összes változását a kijelölt időszakban. Az irányítópult minden módosított entitás esetében felsorolja a következőket:

- Az a számítógép, amelyen a változás történt
- A módosítás típusa (rendszerleíró adatbázis vagy fájl)
- A változás kategóriája (módosítva, hozzáadva, eltávolítva)
- A változás dátuma és időpontja

![A munkaterület módosításai][9]

**A módosítás részletei** akkor nyílnak meg, amikor változást ad meg a keresőmezőben, vagy kijelöl egy entitást a **Változások** lapon.

![Részletek módosítása][10]

## <a name="edit-monitored-entities"></a>Figyelt entitások szerkesztése

1. Térjen vissza a **Fájlintegritás-figyelés idomára,** és válassza a **Beállítások lehetőséget.**

   ![Beállítások][11]

   **A Munkaterület konfigurációja** három lapot jelenít meg: **Windows rendszerleíró adatbázis**, **Windows-fájlok**és **Linux-fájlok**. Minden lap felsorolja az adott kategóriában szerkeszthető entitásokat. A Security Center minden felsorolt entitás esetében azonosítja, hogy a FIM engedélyezve van-e (igaz) vagy nincs-e engedélyezve (hamis).  Az entitás szerkesztése lehetővé teszi a FIM engedélyezését vagy letiltását.

   ![Munkaterület konfigurálása][12]

2. Válasszon identitásvédelmet. Ebben a példában kiválasztottunk egy elemet a Windows rendszerleíró adatbázisában. **Megnyílik a Módosításkövetés szerkesztése.**

   ![Követés szerkesztése vagy módosítása][13]

A **Módosításkövetés szerkesztése** csoportban a következőket teheti:

- (True) vagy letiltás (hamis) fájlintegritás figyelése
- Az entitás nevének biztosítása vagy módosítása
- Az érték vagy elérési út megadásával vagy módosításával
- Az entitás törlése, a módosítás elvetése vagy a módosítás mentése

## <a name="add-a-new-entity-to-monitor"></a>A figyelésre ügyelő új entitás hozzáadása
1. Térjen vissza a **Fájlintegritás figyelési irányítópultjára,** és válassza a **Beállítások** lehetőséget a lap tetején. Megnyílik **a Munkaterület konfigurációja.**
2. A **Munkaterület konfigurálása csoportban**válassza ki a hozzáadni kívánt entitástípus lapját: Windows rendszerleíró adatbázis, Windows-fájlok vagy Linux-fájlok. Ebben a példában a **Linux Files -t**választottuk.

   ![Új elem hozzáadása a monitorozáshoz][14]

3. Válassza a **Hozzáadás** lehetőséget. **Megnyílik a Hozzáadás a változáskövetéshez.**

   ![Adja meg a kért adatokat][15]

4. A **Hozzáadás** lapon írja be a kért adatokat, és válassza a **Mentés gombot.**

## <a name="disable-monitored-entities"></a>Figyelt entitások letiltása
1. Visszatérés a **Fájlintegritás-figyelés** irányítópultra.
2. Válassza ki azt a munkaterületet, ahol a FIM jelenleg engedélyezve van. A FIM számára engedélyezett egy munkaterület, ha hiányzik belőle az Engedélyezés gomb vagy a Frissítési terv gomb.

   ![Olyan munkaterület kiválasztása, ahol a FIM engedélyezve van][16]

3. A Fájlintegritás figyelése csoportban válassza a **Beállítások lehetőséget.**

   ![Beállítások kiválasztása][17]

4. A **Munkaterület konfigurálása csoportban**válasszon ki egy csoportot, amelyben az **Engedélyezve** érték igaz értékre van állítva.

   ![Munkaterület konfigurálása][18]

5. A **Módosításkövetés szerkesztése** ablakbeállítás értéke Hamis értékre **van beállítva.**

   ![Beállítás hamis értékre beállítva][19]

6. Kattintson a **Mentés** gombra.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mappa- és elérésiút figyelése helyettesítő karakterekkel

Helyettesítő karakterek használatával egyszerűsítheti a könyvtárak közötti nyomon követést. A következő szabályok érvényesek, ha helyettesítő karakterek használatával konfigurálja a mappafigyelést:
-   Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
-   Helyettesítő karakterek csak az elérési út utolsó szegmensében használhatók, például C:\folder\file vagy /etc/*.conf
-   Ha egy környezeti változó érvénytelen elérési utat tartalmaz, az érvényesítés sikeres lesz, de az elérési út sikertelen lesz a készlet futtatásakor.
-   Az elérési út beállításakor kerülje az\*általános elérési utakat, például a c: .* elérési utat, amely túl sok mappát eredményez.

## <a name="disable-fim"></a>FiM letiltása
A FIM letiltható. A FIM az Azure Change Tracking megoldás segítségével nyomon követheti és azonosíthatja a környezetváltozásait. A FIM letiltásával eltávolítja a változáskövetés megoldást a kiválasztott munkaterületről.

1. A FIM letiltásához térjen vissza a **Fájlintegritás-figyelés** irányítópultra.
2. Jelöljön ki egy munkaterületet.
3. A **Fájlintegritás figyelése**csoportban válassza **a Letiltás**lehetőséget.

   ![FiM letiltása][20]

4. A letiltáshoz válassza az **Eltávolítás** gombot.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta használni a Fájlintegritás-figyelést (FIM) a Security Centerben. A Biztonsági központról az alábbi oldalakon olvashat bővebben:

* [Biztonsági házirendek beállítása](tutorial-security-policy.md) – Ismerje meg, hogyan konfigurálhatja a biztonsági szabályzatokat az Azure-előfizetésekhez és erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése](security-center-recommendations.md) – Ismerje meg, hogyan segíthetnek a javaslatok az Azure-erőforrások védelmében.
* [Biztonsági állapotfigyelés](security-center-monitoring.md)- Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és megválaszolása](security-center-managing-and-responding-alerts.md)– Ismerje meg, hogyan kezelheti és válaszolhat azokra a biztonsági riasztásokra.
* [Partnermegoldások figyelése](security-center-partner-solutions.md) – Ismerje meg, hogyan figyelheti partnermegoldásainak egészségi állapotát.
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
