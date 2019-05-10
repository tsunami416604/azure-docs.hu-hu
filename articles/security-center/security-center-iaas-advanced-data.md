---
title: Az IaaS az Azure Security Center által nyújtott adatbiztonság speciális |} A Microsoft Docs
description: " Útmutató az Azure Security Center az IaaS lehetővé teszi a speciális biztonsági. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2019
ms.author: monhaber
ms.openlocfilehash: e601bbaa0d15078fc2b19b5b7c536e3a1f6d20ad
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442747"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Speciális biztonsági IaaS-on SQL-kiszolgálók
Az IaaS SQL-kiszolgálók speciális biztonsági egységes csomag egy tapasztalt SQL biztonsági funkciók. Felszínre hozza a és az adatbázis biztonsági réseinek csökkentése és az adatbázis fenyegetést jelezhet rendellenes tevékenységeket észleli a funkció jelenleg tartalmazza.

A szánt IaaS SQL Server-kiszolgálók biztonsági alapul használt ugyanazon alapvető technológia a [Azure SQL Database speciális adatok biztonsági csomag](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Áttekintés

Speciális biztonsági (ADS) biztosít egy speciális SQL biztonsági funkciókat, biztonsági rések felmérése és a komplex veszélyforrások elleni védelem.

* [A biztonságirés-értékelési](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) egy könnyen konfigurálható szolgáltatás, amely a felderítése, nyomon követheti, és segít javítani az adatbázis biztonsági réseinek. A biztonsági állapot rálátást biztosít, és a lépéseket, a biztonsági problémák megoldása és az adatbázis fortifications tartalmazza.
* [Komplex veszélyforrások elleni védelem](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) elérni vagy kiaknázni az SQL server szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek észleli. Folyamatosan figyeli a gyanús tevékenységek esetén az adatbázis és műveletorientált biztonsági riasztásokat biztosít a rendellenes adatbázis-hozzáférési mintákról. Ezek a riasztások adja meg a gyanús tevékenység részleteit, és javasolt műveletek vizsgálata és enyhítése érdekében a fenyegetés.

## <a name="get-started-with-ads-for-iaas"></a>IaaS ADS használatának első lépései

Az alábbi lépéseket az első lépéseket az ADS iaas.

### <a name="set-up-ads-for-iaas"></a>Az IaaS ADS beállítása

**Mielőtt elkezdené**: Szüksége van egy Log Analytics-munkaterületen tárolja a biztonsági naplók elemzése folyamatban. Ha nem rendelkezik egy, akkor a leírtak szerint könnyedén létrehozhat egyet [Log Analytics-munkaterület létrehozása az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Csatlakozzon a virtuális gép a Log Analytics-munkaterületet, az SQL server szoftvert futtatja. Útmutatásért lásd: [csatlakozás Windows számítógépek számára az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Az Azure piactéren, nyissa meg a [SQL Advanced adatok biztonsági megoldás](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Akkor is megtalálhatják azt a Marketplace-en keresési lehetőség használatával, lásd az alábbi képen.) A **SQL biztonságú adatok** lap megnyitásakor.

    ![Az IaaS speciális Adatbiztonság](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Kattintson a **Create** (Létrehozás) gombra. A munkahelyek jelennek meg.

    ![Speciális biztonsági létrehozása](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Válassza ki a munkaterületet használja, és kattintson a **létrehozás**.

   ![Munkaterület kiválasztása](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Indítsa újra a [virtuális gép az SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Ismerje meg, és a biztonsági riasztások vizsgálata

Megtekintheti és kezelheti a jelenlegi biztonsági riasztásokat.

1. Kattintson a **a Security Center** > **biztonsági riasztások**, és kattintson a riasztásra.

    ![Keresse meg a riasztás](./media/security-center-advanced-iaas-data/find-alert.png)

1. Az a **megtámadott erőforrás** oszlopot, kattintson egy erőforrásra, amely a támadás érte.

1. Riasztás részleteinek és az aktuális fenyegetések vizsgálata és a jövőbeli fenyegetésekkel szembeni címkezelés műveleteinek megtekintéséhez görgessen le a **általános információkat** lapot, majd a a **javítási lépések** területén kattintson a a **VIZSGÁLATI lépések** hivatkozásra.

    ![Eltávolítási lépések](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. A riasztás aktiválása társított a naplók megtekintéséhez lépjen a **Log analytics-munkaterületek** , és kövesse az alábbi lépéseket:

     > [!NOTE]
     > Ha **Log analytics-munkaterületek** nem jelennek meg a bal oldali menüben, kattintson a **minden szolgáltatás**, és keressen rá a **Log analytics-munkaterületek**.

    1. Győződjön meg az oszlopok jelennek az **tarifacsomag** és **munkaterület azonosítója** oszlopokat. (**Log analytics-munkaterületek** > **oszlopok szerkesztése**, adjon hozzá **tarifacsomag** és **munkaterület azonosítója**.)

     ![Oszlopok szerkesztése](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Kattintson a munkaterület, amely rendelkezik a riasztási naplókat.

    1. Alatt a **általános** menüben kattintson a **naplók**

    1. Kattintson a Tovább gombra a szem **SQLAdvancedThreatProtection** tábla. A naplók jelennek meg.

     ![Naplók megtekintése](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>ATP riasztásokhoz kapcsolódó E-mail értesítések beállítása 

Beállíthatja a címzettek felkérése az e-mailben értesítést küld az ASC miatti riasztások olyankor jönnek listáját. Az e-mail tartalmazza a riasztás az Azure Security Center az összes releváns részlet közvetlen hivatkozás. 

1. Lépjen a **a Security Center** > **biztonsági házirend** , és kattintson a megfelelő előfizetés sorában **beállítások szerkesztése >**.

    ![Előfizetési beállítások](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Az a **beállítások** menüben kattintson **E-mail-értesítések**. 
1. Az a **E-mail-cím** szöveget adja meg az e-mail-címeket, értesítések fogadására. Az e-mail-címeket vesszővel (,) elválasztva adhat meg egynél több e-mail címet.  Például admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![E-mail beállítások](./media/security-center-advanced-iaas-data/email-settings.png)

1. Az a **e-mailes értesítés** beállításait, adja meg a következő beállításokat:
  
    * **Magas súlyosságú riasztások az e-mail-értesítés küldése**: Ahelyett, hogy az összes riasztás e-mailek küldésekor, csak a magas súlyossági szintű riasztások küldése.
    * **E-mail értesítések is küldhetők az előfizetés-tulajdonosokat**:  Értesítések küldése az előfizetés tulajdonosainak túl.

1. A felső részén a **E-mail-értesítések** kattintson **mentése**.

  > [!NOTE]
  > Ügyeljen arra, hogy kattintson **mentése** előtt bezárja az ablakot, vagy az új **e-mailes értesítés** beállítások nem lesznek mentve.

## <a name="explore-vulnerability-assessment-reports"></a>Sebezhetőségi felmérés jelentések vizsgálata

A biztonságirés-értékelési irányítópult az értékelés eredményeinek áttekintést nyújt az összes adatbázis között. A terjesztési adatbázis SQL Server-verzió és-adatbázisokat, és megfelelően kockázati terjesztési ellenőrzések sikertelenek összefoglalásának sikertelen összegzése együtt megfelelően tekintheti meg.

Megtekintheti a sebezhetőségi felmérés eredmények és a jelentéseket közvetlenül a Log Analytics.

1. Keresse meg az ADS megoldás a Log Analytics-munkaterületet.
1. Navigáljon a **megoldások** , és válassza ki a **SQL-sebezhetőségi felmérés** megoldás.
1. Az a **összefoglalás** ablaktáblán kattintson a **összegzésének megtekintése** , és válassza ki a **SQL biztonsági réseket felmérő jelentés**.

    ![Az SQL jelentés](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    A jelentés irányítópult tölti be. Győződjön meg arról, hogy az időtartomány van állítva, legalább az **legutóbbi 7 nap** óta a vizsgálatot a biztonsági rések értékelése az adatbázisok 7 napon egyszer a meghatározott ütemezés szerint futnak.

    ![Állítsa be az elmúlt 7 napban](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. További részletekért, kattintson az irányítópult elemek. Példa:

   1. Kattintson egy biztonsági rés felvételkor a **sikertelen ellenőrzi az Összegzés** szakaszt az ellenőrzés eredményét a Log Analytics táblázat megtekintéséhez az összes adatbázis között. Az azokat, amelyeket eredmények jelennek meg először.

   1. Kattintson a révén az egyes biztonsági réseket, beleértve a biztonsági rés leírása és a hatása, állapot, kapcsolódó kockázat és ezen az adatbázison a tényleges eredmények a részletek megtekintéséhez. Ezt a jelölőnégyzetet, és a szervizelési információk végrehajtani a biztonsági rés megoldása volt futtatva, a tényleges lekérdezést is látható.

    ![Munkaterület kiválasztása](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Munkaterület kiválasztása](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. A biztonságirés-értékelési eredmények adatokon, az adatok igények szerinti szeleteléséhez és bármely Log Analytics-lekérdezéseket is futtathat.

## <a name="advanced-threat-protection-for-sql-servers-on-iaas-alerts"></a>Komplex veszélyforrások elleni védelem SQL-kiszolgálók az IaaS-riasztások
Szokatlan és vélhetően kárt okozó kísérleteket elérni vagy kiaknázni az SQL Server-kiszolgálók által előállított riasztások. Ezeket az eseményeket is aktiválhatja a következő riasztásokat:

### <a name="anomalous-access-pattern-alerts"></a>Rendellenes hozzáférési minta riasztások

* **Hozzáférés szokatlan helyről:** Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan földrajzi helyről hozzáférési mintájában változik. A lehetséges okok:
     * Egy támadó vagy egy korábbi rosszindulatú alkalmaz érte el az SQL Serverhez.
     * Hiteles felhasználó rendelkezik elérhető az SQL Server új helyét.
* **Hozzáférés potenciálisan káros alkalmazás**: a riasztás akkor aktiválódik, amikor egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. A lehetséges okok:
     * A támadó megpróbálja megsérti a gyakori támadási eszközökkel SQL.
     * Egy megbízható behatolásvizsgálatot kell végezniük a művelet.
* **Hozzáférés résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan résztvevő (SQL-felhasználó) használatával hozzáférési mintájában változik. A lehetséges okok:
     * Egy támadó vagy egy korábbi rosszindulatú alkalmaz érte el az SQL Serverhez. 
     * Egy jogosult felhasználó az SQL Server a érhető el az új egyszerű.
* **Találgatásos támadás SQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha van egy rendellenes magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. A lehetséges okok:
     * A támadó megpróbálja megsérti az SQL találgatásos támadás használatával.
     * Egy megbízható behatolásvizsgálatot kell végezniük a művelet.

### <a name="potential-sql-injection-attacks-coming"></a>Potenciális SQL-injektálási támadások (érkező)

* **Biztonsági rés az SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázis. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A lehetséges okok:
     * Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
     * Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
* **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy olyan azonosított alkalmazás biztonsági rés az SQL-injektálás elleni történik. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
