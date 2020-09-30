---
title: A fájlok integritásának figyelése Azure Security Centerban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a fájlok integritásának figyelését (FIM) Azure Security Center az útmutató segítségével.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b64ff51836f8d291acf57b1cd9ca100c4f87ebed
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541169"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>A fájlok integritásának figyelése Azure Security Center
Ismerje meg, hogyan konfigurálhatja a fájlok integritásának figyelését (FIM) Azure Security Center az útmutató segítségével.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Szükséges szerepkörök és engedélyek:|A **munkaterület tulajdonosa** engedélyezheti vagy letilthatja a FIM-t (További információ: [log Analytics Azure-szerepkörök](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)).<br>Az **olvasó** megtekintheti az eredményeket.|
|Felhők|![Igen ](./media/icons/yes-icon.png) kereskedelmi felhők<br>![Igen ](./media/icons/yes-icon.png) US gov<br>![Nem ](./media/icons/no-icon.png) China gov, más gov<br>Csak olyan régiókban támogatott, ahol a Azure Automation Change Tracking megoldás elérhető.<br>Lásd: [a csatolt log Analytics munkaterület támogatott régiói](../automation/how-to/region-mappings.md).<br>[További információ a Change Tracking szolgáltatásról](../automation/change-tracking.md) |
|||





## <a name="what-is-fim-in-security-center"></a>Mi az a FIM in Security Center?
A fájlok integritásának figyelése (FIM), más néven a változás figyelése, megvizsgálja az operációs rendszer, az alkalmazás szoftvere és mások által a támadásra utaló módosításokat. A rendszer összehasonlító módszert használ annak megállapítására, hogy a fájl aktuális állapota eltér-e a fájl legutóbbi vizsgálatával. Ezt az összehasonlítást kihasználva megállapíthatja, hogy érvényes vagy gyanús módosítások történtek-e a fájlokban.

Security Center a fájl integritásának figyelése ellenőrzi a Windows-fájlok, a Windows-beállításjegyzék és a Linux-fájlok integritását. A FIM engedélyezésével kiválaszthatja a figyelni kívánt fájlokat. Security Center a FIM-mel rendelkező fájlokat figyeli a következő tevékenységekkel:

- Fájl-és beállításjegyzék-létrehozás és-eltávolítás
- Fájl módosításai (a fájlméret változásai, a hozzáférés-vezérlési listán és a tartalom kivonata)
- Beállításjegyzék-módosítások (méret módosítása, hozzáférés-vezérlési listában, típus és tartalom)

Security Center javasolja az entitások figyelését, amelyekkel egyszerűen engedélyezheti a FIM-t. Megadhatja saját FIM-házirendjeit vagy a figyelni kívánt entitásokat is. Ez az útmutató bemutatja, hogyan.

> [!NOTE]
> A Windows és Linux rendszerű számítógépek és virtuális gépek esetében a file Integrity monitoring (FIM) szolgáltatás működik, és csak akkor érhető el, ha az **Azure Defender for Servers** engedélyezve van. További információkért tekintse meg a [díjszabást](security-center-pricing.md) . A FIM feltölti az adatokat a Log Analytics munkaterületre. Az adatforgalmi díjak a feltöltött adatok mennyiségétől függően érvényesek. További információkért tekintse meg a [log Analytics díjszabását](https://azure.microsoft.com/pricing/details/log-analytics/) .

A FIM az Azure Change Tracking megoldást használja a környezet változásainak nyomon követésére és azonosítására. Ha engedélyezve van a fájl integritásának figyelése, akkor egy **megoldás**típusú **change Tracking** erőforrással rendelkezik. Az adatgyűjtési gyakoriság részleteivel kapcsolatban lásd az Azure Change Trackingre vonatkozó [változáskövetési adatgyűjtés részleteit](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details).

> [!NOTE]
> Ha eltávolítja a **change Tracking** -erőforrást, a Security Center a fájl sértetlenségének figyelése funkciót is letiltja.

## <a name="which-files-should-i-monitor"></a>Mely fájlokat kell figyelni?
Gondolja át a rendszer és az alkalmazások szempontjából kritikus fontosságú fájlokat a figyelni kívánt fájlok kiválasztásakor. Érdemes lehet olyan fájlokat választania, amelyeket nem várható a tervezés nélkül módosítani. Az alkalmazások vagy az operációs rendszer által gyakran módosított fájlok (például a naplófájlok és szövegfájlok) kiválasztásával nagy mennyiségű zaj hozható létre, ami megnehezíti a támadás azonosítását.

A Security Center az alábbi, az ismert támadási minták alapján figyelt elemek listáját tartalmazza. Ezek közé tartoznak a fájlok és a Windows-beállításkulcsok. A kulcsok az HKEY_LOCAL_MACHINE (HKLM) alatt találhatók a táblában.)

|**Linux-fájlok**|**Windows-fájlok**|**Windows-beállításkulcsok**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \Boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows CurrentVersion|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|Hklmsoftwaremicrosoftwindowscurrentversionexplorershell\\\\ mappák|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj-mappák|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin helyekre|C:\Program Files\Microsoft biztonsági Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \Boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows CurrentVersion|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell mappák|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj-mappák|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>A fájlok integritásának figyelésének engedélyezése 

1. Az **Azure Defender** irányítópultjának **speciális védelmi** területén válassza a **fájl integritásának figyelése**elemet.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="FIM indítása" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    Megnyílik a **fájl integritásának figyelése** konfiguráció oldal.

    Az egyes munkaterületek esetében az alábbi információk szerepelnek:

    - A múlt héten bekövetkezett módosítások teljes száma (egy kötőjelet láthat, ha a FIM nincs engedélyezve a munkaterületen)
    - A munkaterületnek jelentett számítógépek és virtuális gépek teljes száma
    - A munkaterület földrajzi helye
    - Az Azure-előfizetés, amelyre a munkaterület tartozik

1. Ezen a lapon a következőket használhatja:

    - Az egyes munkaterületek állapotának és beállításainak elérése és megtekintése

    - ![A Frissítési terv ikonja ][4] frissíti a munkaterületet az Azure Defender használatára. Ez az ikon azt jelzi, hogy a munkaterületet vagy előfizetést nem az Azure Defender védi. A FIM-funkciók használatához az előfizetését az Azure Defendernek kell védenie. [További információ](security-center-pricing.md).

    - ![Engedélyezés ikon][3] Engedélyezze a FIM-t a munkaterület alá tartozó összes gépen, és konfigurálja a FIM beállításait. Ez az ikon azt jelzi, hogy a FIM nincs engedélyezve a munkaterületen.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="FIM indítása":::


    > [!TIP]
    > Ha nincs engedélyezve vagy frissítés gomb, és a szóköz üres, az azt jelenti, hogy a FIM már engedélyezve van a munkaterületen.


1. Válassza az **Engedélyezés**lehetőséget. A munkaterület részletei, beleértve a munkaterületen található Windows és Linux rendszerű gépek számát is.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM indítása":::

   A Windows és a Linux ajánlott beállításai is szerepelnek a felsorolásban.  Bontsa ki a **Windows-fájlok**, a **beállításjegyzék**és a **Linux-fájlok** elemet a javasolt elemek teljes listájának megtekintéséhez.

1. Törölje a jelölőnégyzeteket azokhoz az ajánlott entitásokhoz, amelyeket nem szeretne a FIM által figyelni.

1. A FIM engedélyezéséhez válassza a **Fájlszűrő-figyelés alkalmazása** lehetőséget.

> [!NOTE]
> Bármikor módosíthatja a beállításokat. További információért lásd: a [figyelt entitások szerkesztése](#edit-monitored-entities) .



## <a name="audit-monitored-workspaces"></a>Figyelt munkaterületek naplózása 

A **fájl integritás-figyelési** irányítópultja megjeleníti azokat a munkaterületeket, amelyeken engedélyezve van a FIM. A FIM-irányítópult megnyílik, miután engedélyezte a FIM használatát egy munkaterületen, vagy ha kiválaszt egy munkaterületet a **fájl integritásának figyelése** ablakban, amelyen már engedélyezve van a FIM.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM indítása":::

A munkaterület FIM-irányítópultja a következő adatokat jeleníti meg:

- A munkaterülethez csatlakozó számítógépek teljes száma
- A kiválasztott időszakban történt módosítások teljes száma
- A változás típusának részletezése (fájlok, beállításjegyzék)
- A módosítási kategória részletezése (módosított, hozzáadott, eltávolított)

Az irányítópult tetején található **szűrő** lehetőségre kattintva módosíthatja azt az időtartamot, ameddig a módosítások megjelennek.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="FIM indítása":::

A **kiszolgálók** lap felsorolja a munkaterületnek jelentett gépeket. Az irányítópultok az egyes gépeknél a következőket sorolja fel:

- A kiválasztott időszakban történt összes módosítás
- A módosítások vagy a beállításjegyzék változásainak teljes változásainak részletezése

A **naplóbeli keresés** akkor nyílik meg, amikor beírja a számítógép nevét a keresőmezőbe, vagy kiválasztja a számítógépek lapon felsorolt gépet. a naplóbeli keresés megjeleníti a számítógép kiválasztott időszakában végrehajtott összes módosítást. További információért bontsa ki a változást.

![Naplók keresése][8]

A **módosítások** lap (lásd alább) megjeleníti a munkaterület összes változását a kiválasztott időszakban. Minden módosított entitás esetében az irányítópult a következőket sorolja fel:

- A számítógép, amelyen a módosítás történt
- Változás típusa (beállításjegyzék vagy fájl)
- Változás kategóriája (módosított, hozzáadott, eltávolított)
- A módosítás dátuma és időpontja

![A munkaterület módosításai][9]

A **change details** (módosítás részletei) elemre kattintva megadhatja a keresés mezőben megjelenő változást, vagy kijelölhet egy entitást a **módosítások** lapon.

![Változás részletei][10]

## <a name="edit-monitored-entities"></a>Figyelt entitások szerkesztése

1. Térjen vissza a **fájl integritás-figyelési irányítópultra** , és válassza a **Beállítások**lehetőséget.

   ![Beállítások][11]

   A **munkaterület-konfiguráció** három lapot jelenít meg: **Windows beállításjegyzék**, **Windows-fájlok**és Linux- **fájlok**. Minden lap felsorolja az adott kategóriában szerkeszthető entitásokat. Az egyes felsorolt entitások esetében Security Center azonosítja, hogy a FIM engedélyezve van-e (true) vagy nincs engedélyezve (false).  Az entitás szerkesztése lehetővé teszi a FIM engedélyezését vagy letiltását.

   ![Munkaterület konfigurációja][12]

2. Válasszon ki egy Identity Protectiont. Ebben a példában egy elemet választottunk a Windows beállításjegyzékben. Megnyílik **a Change Tracking szerkesztése** .

   ![Szerkesztés vagy módosítás nyomon követése][13]

**A Change Tracking szerkesztése területen a** következőket teheti:

- A fájl integritás-figyelésének engedélyezése (true) vagy Disable (false)
- Adja meg vagy módosítsa az entitás nevét
- Adja meg vagy módosítsa az értéket vagy az elérési utat
- Törölje az entitást, vesse el a módosítást, vagy mentse a módosítást.

## <a name="add-a-new-entity-to-monitor"></a>Új entitás hozzáadása a figyeléshez
1. Térjen vissza a **fájl integritás-figyelési irányítópultra** , és válassza a felül található **Beállítások** lehetőséget. Megnyílik a **munkaterület konfigurációja** .
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

   ![Munkaterület konfigurációja][18]

5. A **szerkesztés alatt Change Tracking** az ablak értéke false (hamis) értékre **van** állítva.

   ![Beállítás engedélyezve hamis értékre][19]

6. Kattintson a **Mentés** gombra.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mappa és elérési út figyelése helyettesítő karakterek használatával

Használjon helyettesítő karaktereket a címtárak közötti nyomkövetés egyszerűsítéséhez. A következő szabályok akkor érvényesek, ha a mappák figyelését helyettesítő karakterek használatával konfigurálja:
-   Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
-   Helyettesítő karakterek csak az elérési út utolsó szegmensében használhatók, például C:\folder\file vagy/etc/*. conf
-   Ha egy környezeti változó érvénytelen elérési utat tartalmaz, az érvényesítés sikeres lesz, de az elérési út sikertelen lesz a leltár futtatásakor.
-   Az elérési út beállításakor kerülje el az általános elérési utakat, például a c: \* . * értéket, ami túl sok mappát fog áthaladni.

## <a name="disable-fim"></a>FIM letiltása
A FIM letiltható. A FIM az Azure Change Tracking megoldást használja a környezet változásainak nyomon követésére és azonosítására. A FIM letiltásával eltávolítja a Change Tracking megoldást a kiválasztott munkaterületről.

1. A FIM letiltásához térjen vissza a **fájl integritásának figyelése** irányítópultra.
2. Jelöljön ki egy munkaterületet.
3. A **fájl integritásának figyelése**területen válassza a **Letiltás**lehetőséget.

   ![FIM letiltása][20]

4. A letiltáshoz válassza az **Eltávolítás** lehetőséget.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a fájlok integritásának figyelését (FIM) a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő lapokat:

* [Biztonsági házirendek beállítása](tutorial-security-policy.md) – megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
* [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Azure biztonsági blog](https://docs.microsoft.com/archive/blogs/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
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