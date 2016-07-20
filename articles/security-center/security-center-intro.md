<properties
   pageTitle="Az Azure Security Center bemutatása | Microsoft Azure"
   description="Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="terrylan"/>

# Az Azure Security Center bemutatása

Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről.

> [AZURE.NOTE] A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak. Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.

## Mi az Azure Security Center?
 A Security Center az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

##  Főbb funkciók
 A Security Center az Azure portál beépített funkcióiként biztosítja a fenyegetések egyszerű és hatékony megelőzését, észlelését és az azokra való reagálást. A főbb funkciók a következők:

| | |
|----- |-----|
| Megelőzés | Az Azure-erőforrások biztonsági állapotának monitorozása |
| | A vállalati biztonsági követelmények, a használt alkalmazások típusa és az adatok érzékenysége alapján szabályzatokat határoz meg az Azure-előfizetésekre és -erőforráscsoportokra |
| | A szabályzaton alapuló biztonsági javaslatok használatával végigvezeti a szolgáltatások tulajdonosait a szükséges szabályozási folyamat végrehajtásán |
| | Gyorsan telepíti a Microsoft és a partnerei biztonsági szolgáltatásait és készülékeit |
| Észlelés |Automatikusan gyűjti és elemzi az Azure-erőforrások, a hálózat és a partneri megoldások, például kártevőirtó-programok és tűzfalak biztonsági adatait |
| | Használja a Microsoft-termékek és -szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC), valamint külső hírcsatornák globális fenyegetésészlelési adatait |
| | Bővített analitikát alkalmaz, beleértve a gépi tanulást és a viselkedéselemzést |
| Válasz | Rangsorolt biztonsági eseményeket/riasztásokat tartalmaz |
| | Áttekintést nyújt a támadás forrásáról és az érintett erőforrásokról |
| | Módszereket javasol a fennálló fenyegetés megszüntetésére és a jövőbeli fenyegetések megelőzésre |

## Bevezető áttekintés
 A Security Center az [Azure portálról](https://azure.microsoft.com/features/azure-portal/) érhető el. [Jelentkezzen be a portálra](https://portal.azure.com), válassza a **Tallózás** lehetőséget, majd görgessen a **Security Center** elemhez, vagy válassza a portál irányítópultján korábban már rögzített **Security Center** csempét.

![Biztonság csempe az Azure portálon][1]

A Security Centerből biztonsági szabályzatokat állíthat be, figyelheti a biztonsági konfigurációkat, és megtekintheti a biztonsági riasztásokat.

### Biztonsági szabályzatok

A vállalat biztonsági követelményeinek megfelelően szabályzatokat definiálhat az Azure-előfizetésekhez és -erőforráscsoportokhoz. Ezeket testre is szabhatja az adott alkalmazás típusa vagy az egyes előfizetések adatainak érzékenysége alapján. Például különböző biztonsági követelmények vonatkozhatnak a fejlesztésben vagy tesztelésben, illetve az éles környezetben használt erőforrásokra. A szabályozott adatokkal (például személyes adatokkal) működő alkalmazások is magasabb szintű biztonságot követelhetnek meg.

> [AZURE.NOTE] Kizárólag az előfizetés tulajdonosa vagy közreműködője módosíthatja a biztonsági szabályzatot az előfizetés vagy az erőforráscsoport szintjén.

A **Security Center** panelen válassza az előfizetések és az erőforráscsoportok kívánt listájához tartozó **Szabályzat** csempét.   

![Security Center panel][2]

A **Biztonsági szabályzat** panelen válasszon ki egy előfizetést a szabályzat részleteinek megtekintéséhez.

![Biztonsági szabályzat panel: előfizetés][3]

Az **Adatgyűjtés** (lásd fent) lehetővé teszi a biztonsági szabályzatok számára történő adatgyűjtést. Az engedélyezés esetén a következő műveletekre kerül sor:

- Az összes támogatott virtuális gép biztonsági ellenőrzésének napi vizsgálata és javaslatok.
- Biztonsági események gyűjtése elemzési célokra és a fenyegetések észlelésére.

A **Tárfiók régiónkénti kiválasztása** beállítás (lásd fent) lehetővé teszi, hogy minden olyan régióhoz, ahol vannak virtuális gépek az előfizetéshez, kiválaszthat egy tárfiókot, ahol az adott régió virtuális gépeiről gyűjtött adatok tárolásra kerülnek. Ha nem ad meg tárfiókot az egyes régiókhoz, a rendszer automatikusan létrehozza azt. A gyűjtött adatokat biztonsági okokból más ügyfelek adataitól logikailag elkülönítve tároljuk.

> [AZURE.NOTE] Az adatgyűjtést és a tárfiók régiónkénti kiválasztását az előfizetések szintjén kell konfigurálni.

A **Javaslatok megjelenítése** beállítás (lásd fent) lehetővé teszi, hogy kiválassza azokat a biztonsági ellenőrzéseket, amelyeket az előfizetésen belüli erőforrások biztonsági igényei alapján vizsgálni akar, és amelyekre nézve javaslatokat akar kapni.

Ezután válasszon ki egy erőforráscsoportot a szabályzat részleteinek megtekintéséhez.

![Biztonsági szabályzat panel: erőforráscsoport][4]

Az **Öröklés** funkcióval (lásd fent) meghatározhatja az erőforráscsoportot a következőként:

- Örökölt (alapértelmezett), ami azt jelenti, hogy ez az erőforráscsoport az összes biztonsági szabályzatot örökli az előfizetés szintjéről.
- Egyedi, ami azt jelenti, hogy az erőforráscsoportnak egyéni biztonsági szabályzata lesz. Változtatásokat kell eszközölnie a **Javaslatok megjelenítése** alatt.

> [AZURE.NOTE] Az előfizetés szintű és az erőforráscsoport szintű szabályzat közötti ütközés esetén az erőforráscsoport szintű szabályzat élvez elsőbbséget.

### Biztonsági javaslatok

 A Security Center a potenciális biztonsági hiányosságok azonosítása érdekében elemzi az Azure-erőforrások biztonsági állapotát. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán. Példák erre vonatkozóan:

- Kártevőirtók kiépítése a kártékony szoftverek azonosításához és eltávolításához
- Hálózati biztonsági csoportok konfigurálása és a virtuális gépek hálózati forgalmának ellenőrzésére vonatkozó szabályok konfigurálása
- Webalkalmazási tűzfalak kiépítése a webalkalmazások ellen irányuló támadások elleni védelemre
- Hiányzó rendszerfrissítések telepítése
- Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Kattintson a **Javaslatok** csempére a javaslatok listájának megtekintéséhez. Válasszon ki egy javaslatot a további információk megjelenítéséhez vagy a probléma megoldásához.

![Biztonsági javaslatok az Azure Security Centerben][5]

### Erőforrás állapota

Az **Erőforrás biztonsági állapota** csempe a környezet általános biztonsági állapotát mutatja meg erőforrástípusok szerint, beleértve a virtuális gépeket, a webalkalmazásokat és az egyéb erőforrásokat.   

Az **Erőforrás biztonsági állapota** csempén válasszon ki egy erőforrástípust a további információk (köztük az azonosított potenciális biztonsági hiányosságok listájának) megtekintéséhez. (A lenti példában a **Virtuális gépek** van kiválasztva.)

![Az Erőforrások állapota csempe][6]

### Biztonsági riasztások

 A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és az olyan partnermegoldások, mint például a kártevőirtó-programok és a tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

- Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
- Windows hibajelentés által észlelt speciális kártevő
- Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
- Az integrált kártevőirtó programok és a tűzfalak biztonsági riasztásai

A **Biztonsági riasztások** csempére kattintva megjelenítheti a rangsorolt riasztásokat.

![Biztonsági riasztások][7]

Egy riasztás kiválasztásával további információkat tekinthet meg a támadásról, valamint javaslatokat kap a támadás elhárítására vonatkozóan.

![Biztonsági figyelmeztetés részletei][8]

### Partneri megoldások

A **Partneri megoldások** csempén áttekintheti az Azure-előfizetésében integrált partneri megoldások biztonsági állapotát. A Security Center itt a partnermegoldások riasztásait jeleníti meg.

Válassza a **Partneri megoldások** csempét. Ekkor megnyílik egy panel, amely a központtal összekapcsolt partnermegoldások listáját tartalmazza.

![Partneri megoldások][9]

## Első lépések
A Security Center használatához rendelkeznie kell Microsoft Azure- előfizetéssel. A Security Center engedélyezve van Azure- előfizetéséhez. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).

 A Security Center az [Azure portálról](https://azure.microsoft.com/features/azure-portal/) érhető el. További információt a [portál dokumentációjában](https://azure.microsoft.com/documentation/services/azure-portal/) talál.

Az [Ismerkedés az Azure Security Centerrel](security-center-get-started.md) segítségével gyorsan megismerheti a Security Center biztonságfelügyeleti és szabályzat-kezelési összetevőit.

## Következő lépések
Ebben a dokumentumban megismerhette a Security Centert, annak főbb funkcióit és használatának első lépéseit. További információ:

- [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
- [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
- [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
- [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
- [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png



<!--HONumber=Jun16_HO2-->


