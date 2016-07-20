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
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Mélyebb betekintés az Azure Security Center adataiba a Power BI segítségével
Az Azure Security Center [Power BI irányítópultja](http://aka.ms/azure-security-center-power-bi) lehetővé teszi a javaslatok és biztonsági riasztások megjelenítését, elemzését és szűrését bárhonnan, akár a mobileszközökről is. A Power BI irányítópult segítségével feltárhatja a trendeket és támadási mintákat - tekintse meg a biztonsági riasztásokat az erőforrás vagy a forrás IP-címe szerint, a cím nélküli biztonsági kockázatokat pedig erőforrás vagy kor szerint. A Security Center javaslatait és biztonsági riasztásait érdekes módokon vegyítheti más adatokkal, például a Power BI irányítópulttal rendelkező [Azure naplókkal](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) vagy [Azure SQL Database naplózási szolgáltatással](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), vagy exportálhatja az adatokat Excelbe a felhőalapú erőforrások biztonsági állapotára vonatkozó jelentések egyszerű elkészítése érdekében.

> [AZURE.NOTE] A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak.


##Az Azure Security Center irányítópultjának használata a Power BI eléréséhez
Az Azure Security Center irányítópultjának használatával is elérheti a Power BI jelentéseket. A feladat végrehajtásához hajtsa végre az alábbi lépéseket: 

1. Az **Azure Security Center** irányítópultján kattintson a **Power BI: böngészés** gombra.

    ![Csatlakozás az Azure Security Centerhez Power BI használatával](./media/security-center-powerbi/security-center-powerbi-fig9-new.png) 

2. A **Power BI: böngészés** panel az alább látható módon megnyílik a jobb oldalon:

    ![Csatlakozás az Azure Security Centerhez Power BI használatával](./media/security-center-powerbi/security-center-powerbi-fig2-new.png)

3. Ha első alkalommal hoz létre Power BI irányítópultot, az alábbi lehetőségek közül választhat a Power BI: böngészés panelen: 

    - **Biztonság irányítópult**: válassza ezt a beállítást, ha olyan irányítópultot szeretne létrehozni, amely a biztonsági állapotot, a szálakat és az észleléseket tartalmazza. Ez a beállítás gyakori a DevOps szerepkör esetén, amely a védelmi állapot elemzéséért és a különböző előfizetéseknél észlelt riasztásokért felelős.
    - **Házirend-kezelési irányítópult**: válassza ezt a beállítást, ha a kezelési és kényszerítési házirendet szeretné használni.  Ez a beállítás gyakori a központi IT-felügyelet esetén, ahol inkább a cégirányításra fókuszálnak. Ezzel az irányítópulttal láthatóvá tehetik és áttekinthetik a biztonsági házirendnek való megfelelést a szervezet egészére nézve.
    - Ha már rendelkezik Power BI irányítópulttal, kattintson az **Aktuális Power BI irányítópult megnyitása** lehetőségre.

4. A jelen példában kattintson a **Biztonság irányítópult** lehetőségre, ekkor megjelenik az alábbi ablak:

    ![Azure Security Center Biztonság irányítópult](./media/security-center-powerbi/security-center-powerbi-fig3-new.png)

5. Győződjön meg arról, hogy **Hitelesítési módszernek** az **oAuth2** van beállítva, és kattintson a **Bejelentkezés** lehetőségre.
6. Ekkor megnyílik a **Power BI** ablak, és egy hasonló struktúrájú jelentést fog látni:
    
    ![Biztonság irányítópult](./media/security-center-powerbi/security-center-powerbi-fig5.png)

> [AZURE.NOTE] A jelentés napi frissítésre van beállítva, ha a frissítésben hibát tapasztal, olvassa el az [Azure Security Center Power BI frissítésével kapcsolatos potenciális problémák](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) részt a hiba elhárításának részleteiért.

Itt láthatja a biztonsági riasztások és javaslatok számát, a virtuális gépek számát, valamint az Azure Security Center által figyelt Azure SQL adatbázisokat és hálózati erőforrásokat.

Az Azure Security Center-re mutató hivatkozás átirányítja az Azure portálra. A diagramokkal könnyen megjeleníthetők a biztonsági javaslatokra és riasztásokra vonatkozó információk, beleértve a következőket:

- Erőforrás biztonság állapota
- Függőben lévő javaslatok, általános
- Virtuális gépekre vonatkozó javaslatok
- Riasztások adott idő alatt
- Megtámadott erőforrások
- Megtámadott IP-címek

Minden egyes diagram mögött további elemzések vannak. Válasszon ki egy csempét a további információk megtekintéséhez. Például az Erőforrás biztonsági állapota csempe kiválasztásával további részleteket tudhat meg a függőben lévő javaslatokról erőforrásonként, ahogy az alábbiakban látható:

![Javaslatok](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Ha ennek a grafikonnak bármely sorára kattint, a többi sor kiszürkül, így kizárólag a kijelölt sorra tud koncentrálni. Az irányítópultra való visszatéréshez kattintson az **Azure Security Center** lehetőségre az **Irányítópultok** lehetőség alatt az oldal bal oldali ablaktáblájában.

> [AZURE.NOTE] Ha további mezők hozzáadásával vagy a meglévő látványelemek módosításával szeretné jelentéseit testre szabni, szerkesztheti a jelentést. További információkért olvassa el a [Kommunikáció egy jelentéssel a Power BI szerkesztés nézetében](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) részt.

A **Riasztások adott idő alatt, Megtámadott erőforrások** és **Támadó IP-címek** csempékre kattintva is találhat további információkat. Ez azért van, mert a jelentés **Támadás alatt lévő erőforrások** néven összesíti  a három változóra vonatkozó információkat az alább látható módon:

![Támadás alatt lévő erőforrások](./media/security-center-powerbi/security-center-powerbi-fig7.png)

Ekkor a **Fájl** menü segítségével mentheti, kinyomtathatja vagy közzéteheti a weben a jelentés másolatát.

![Fájl menü](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## Az Azure Security Center adatainak felfedezése Power BI segítségével

Csatlakozzon a [Power BI tartalomcsomag szolgáltatásokhoz](https://msit.powerbi.com/groups/me/getdata/services) a Power BI irányítópulton, és kövesse az alábbi lépéseket:

1. A **Power BI tartalomcsomag** ablakban az alábbi két lehetőség közül választhat.

    ![Power BI tartalomcsomag](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

2. A jelen példában kattintson a **Lekérés** lehetőségre az **Azure Security Center Házirendkezelés** csempéjén.

3. A  **Csatlakozás az Azure Security Center Házirendkezeléshez** ablakban a lent látható módon válassza az **oAuth2** lehetőséget a **Hitelesítési módszer** legördülő menüben, majd kattintson a **Bejelentkezés** gombra.

    ![Házirendkezelés ablak](./media/security-center-powerbi/security-center-powerbi-fig4-new.png)

4. A rendszer egy hitelesítési oldalra irányítja, ahol meg kell adnia a szükséges hitelesítő adatokat az Azure Security Centerhez való kapcsolódáshoz. A hitelesítési folyamat befejezése után a Power BI elindítja az adatok importálását a jelentések készítéséhez. Eközben a böngésző jobb felső sarkában a következő üzenetet láthatja:

    ![Csatlakozás az Azure Security Centerhez Power BI használatával](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Ha első alkalommal hoz létre irányítópultot, akkor ez a szokásosnál tovább tarthat, különösen ha több előfizetése van. 

5. A folyamat befejezése után az Azure Security Center Power BI irányítópultja betölti a **Házirendkezelés** jelentést.


## Következő lépések
Ebben a dokumentumban megismerkedhetett az Azure Security Center Power BI irányítópultjának használatával. A Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Annak bemutatása, hogy miként tervezheti meg az Azure Security Center bevezetését.
- [Biztonsági házirendek beállítása az Azure Security Centerben](security-center-policies.md) – Annak bemutatása, hogy miként konfigurálhatja az Azure Security Center biztonsági beállításait
- [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Annak bemutatása, hogy miként kezelheti a biztonsági riasztásokat, és hogyan reagálhat azokra.
- [Azure Security Center: GYIK](security-center-faq.md) – Gyakori kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelési funkcióiról



<!--HONumber=Jun16_HO2-->


