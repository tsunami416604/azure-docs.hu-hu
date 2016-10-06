<properties
   pageTitle="Mélyebb betekintés az Azure Security Center adataiba a Power BI segítségével| Microsoft Azure"
   description="Az Azure Security Center Power BI tartalomcsomaggal a jelentéskészítéshez létrehozott adatkészlet alapján könnyen megtalálhatja a biztonsági riasztásokat, a javaslatokat, a megtámadott erőforrásokat és a trendeket."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>


# Mélyebb betekintés az Azure Security Center adataiba a Power BI segítségével
Az Azure Security Center [Power BI irányítópultja](http://aka.ms/azure-security-center-power-bi) lehetővé teszi a javaslatok és biztonsági riasztások megjelenítését, elemzését és szűrését bárhonnan, akár a mobileszközökről is. A Power BI irányítópult segítségével feltárhatja a trendeket és támadási mintákat - tekintse meg a biztonsági riasztásokat az erőforrás vagy a forrás IP-címe szerint, a cím nélküli biztonsági kockázatokat pedig erőforrás vagy kor szerint. 

A Security Center javaslatainak és a biztonsági riasztásoknak más adatokkal történő összefűzésének is számos érdekes megoldása létezik, így például az [Azure vizsgálati naplók](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) és az [Azure SQL Database Auditing](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/) adatainak együttes felhasználása. Mindkettő lehetővé teszi a Power BI-irányítópultok használatát, a felhőerőforrások biztonsági állapotáról történő egyszerű jelentéskészítéshez pedig az adatok Excel formátumba is exportálhatók.

##Az Azure Security Center irányítópultjának használata a Power BI eléréséhez
Az Azure Security Center irányítópultjának használatával is elérheti a Power BI jelentéseket. Kövesse az alábbi lépéseket a feladat végrehajtásához: 

1. Az **Azure Security Center** irányítópultján kattintson a **Power BI: böngészés** gombra.

    ![Csatlakozás az Azure Security Centerhez Power BI használatával](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. A **Kezelés a Power BI-ban** panel a jobb oldalon nyílik meg, ahogy az alábbi képernyőn látható:

    ![Csatlakozás az Azure Security Centerhez Power BI használatával](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Ha első alkalommal hoz létre Power BI-irányítópultot, a **Kezelés a Power BI-ban** panelen az alábbi beállítások egyikét választhatja ki: 

    - **Security insights irányítópult**: akkor válassza ezt a beállítást, ha olyan irányítópultot szeretne létrehozni, amely a biztonsági állapotot, a szálakat és az észleléseket tartalmazza. E beállítás használata a DevOps szerepkör esetében gyakoribb, amely a védelmi állapot és az észlelt riasztások előfizetések közötti elemzését végzi.
    - **Házirend-kezelési irányítópult**: válassza ezt a beállítást, ha a kezelési és kényszerítési házirendet szeretné használni.  E beállítás az irányítás-központúbb központi informatikai alkalmazások esetében gyakoribb. Ezzel az irányítópulttal láthatóvá tehetik és áttekinthetik a biztonsági házirendnek való megfelelést a szervezet egészére nézve.
    - Ha már rendelkezik Power BI irányítópulttal, kattintson az **Aktuális Power BI irányítópult megnyitása** lehetőségre.

4. E példában kattintson a **Security insights irányítópult** lehetőségre. Ha először hoz létre Power BI-irányítópultot a Security Center számára, a rendszer felkéri a tartalomcsomag telepítésére. Az alábbi képernyőn látható módon kattintson a **Lekérés** gombra a **Power BI-tartalomcsomagok** ablakban:

    ![Azure Security Center Security Insights irányítópult](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. Megjelenik a **Connect to Azure Security Center Security Insights** (Csatlakozás az Azure Security Center Security Insights irányítópulthoz) ablak. Győződjön meg arról, hogy a beállított **Hitelesítési** módszer az **oAuth2**, majd kattintson a **Bejelentkezés** gombra.
    
    ![Authentication](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. A rendszer felkérheti az Azure rendszerre vonatkozó hitelesítő adatok újbóli megadására. A hitelesítést követően létrejön az irányítópult. Az irányítópult létrehozása után egy, az alábbi képernyőn láthatóhoz hasonló szerkezetű jelentés jelenik meg:

    ![Power BI-irányítópult](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] A beállított ütemezés szerint a jelentés naponta frissül. Ha a frissítés meghiúsul, további hibaelhárítási útmutatásért olvassa el a következő cikket: [Az Azure Security Center Power BI lehetséges frissítési problémái](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/).

Itt láthatja a biztonsági riasztások és javaslatok számát, a virtuális gépek számát, valamint az Azure Security Center által figyelt Azure SQL-adatbázisokat és hálózati erőforrásokat.

Az Azure Security Center-re mutató hivatkozás átirányítja az Azure Portalra. A diagramokkal könnyen megjeleníthetők a biztonsági javaslatokra és riasztásokra vonatkozó információk, beleértve a következőket:

- Erőforrás biztonsági állapota
- Függőben lévő javaslatok
- Virtuális gépekre vonatkozó javaslatok
- Riasztások adott idő alatt
- Megtámadott erőforrások
- Megtámadott IP-címek

Minden egyes diagram mögött további elemzések találhatók. Válasszon ki egy csempét a további információk megtekintéséhez. Ha például a **Resource Security State** (Erőforrás biztonsági állapota) csempén további részletek láthatóak a függőben lévő erőforrás-javaslatokról, ahogyan az az alábbi képernyőn látható:

![Javaslatok](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Ha az ábra bármely vonalára rákattint, a többi vonal kiszürkül, így a kiválasztott vonalra koncentrálhat. Az irányítópultra való visszatéréshez kattintson az **Azure Security Center** lehetőségre az **Irányítópultok** lehetőség alatt az oldal bal oldali ablaktáblájában.

> [AZURE.NOTE] Ha további mezők hozzáadásával vagy a meglévő látványelemek módosításával szeretné jelentéseit testre szabni, szerkesztheti a jelentést. További információkért olvassa el a [Kommunikáció egy jelentéssel a Power BI szerkesztés nézetében](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) részt.

A **Riasztások adott idő alatt, Megtámadott erőforrások** és **Támadó IP-címek** csempékre kattintva is találhat további információkat. Ez azért történik, mert a jelentés mindhárom változóval kapcsolatos információkat összesíti, és **Támadás alatt lévő erőforrások** név alatt jeleníti meg, ahogy a következő képernyőn is látható:

![Támadás alatt lévő erőforrások](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

Ekkor a **Fájl** menü segítségével mentheti, kinyomtathatja vagy közzéteheti a weben a jelentés másolatát.

![Fájl menü](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## Az Azure Security Center adatainak felfedezése Power BI segítségével

A Power BI-ban csatlakozzon a [Power BI-tartalomcsomagszolgáltatásokhoz](https://msit.powerbi.com/groups/me/getdata/services), és hajtsa végre a következő lépéseket:

1. A **Power BI tartalomcsomag** ablakban az alábbi két lehetőség közül választhat.

    ![Power BI tartalomcsomag](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Ha a cikk első részében foglaltakat már végrehajtotta, itt csak egy lehetőséget fog látni, amely az Azure Security Center házirendkezelése.

2. A jelen példában kattintson a **Lekérés** lehetőségre az **Azure Security Center Házirendkezelés** csempéjén.

3. A  **Csatlakozás az Azure Security Center Házirendkezeléshez** ablakban a lent látható módon válassza az **oAuth2** lehetőséget a **Hitelesítési módszer** legördülő menüben, majd kattintson a **Bejelentkezés** gombra.

    ![Házirendkezelés ablak](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. A rendszer egy hitelesítési oldalra irányítja, ahol meg kell adnia a szükséges hitelesítő adatokat az Azure Security Centerhez való kapcsolódáshoz. A hitelesítési folyamat befejezése után a Power BI elindítja az adatok importálását a jelentések készítéséhez. Eközben a böngésző jobb felső sarkában a következő üzenetet láthatja:

    ![Csatlakozás az Azure Security Centerhez Power BI használatával](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Ha első alkalommal hoz létre irányítópultot, akkor ez a szokásosnál tovább tarthat, különösen ha több előfizetése van. 

5. Ha a folyamat befejeződött, az Azure Security Center Power BI-irányítópultja a **Házirendkezelés** jelentéssel együtt fog betöltődni, az alábbi képen láthatóhoz hasonló módon:

    ![A Házirendkezelés irányítópultja](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## Lásd még:
Ebben a dokumentumban megismerkedhetett az Azure Security Center Power BI irányítópultjának használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Annak bemutatása, miként tervezheti meg az Azure Security Center bevezetését.
- [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Sep16_HO4-->


