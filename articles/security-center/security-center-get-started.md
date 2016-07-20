<properties
   pageTitle="Bevezetés az Azure Security Center használatába | Microsoft Azure"
   description="Ez az Azure Security Center használatának gyors megkezdéséhez segítséget nyújtó dokumentum ismerteti a biztonságfelügyeleti és a szabályzatkezelési összetevőket, valamint felsorolja a kapcsolódó témaköröket."
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
   ms.date="04/26/2016"
   ms.author="terrylan"/>

# Bevezetés az Azure Security Center használatába

Ez az Azure Security Center használatának gyors megkezdéséhez segítséget nyújtó dokumentum ismerteti a biztonságfelügyeleti és a szabályzatkezelési összetevőket, valamint felsorolja a kapcsolódó témaköröket.

> [AZURE.NOTE] A jelen dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak. Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A dokumentum nem tartalmaz lépésenkénti útmutatót.

## Mi az Azure Security Center?
 A Security Center az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

## Előfeltételek

A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Az előfizetés magában foglalja a Security Center használatának lehetőségét. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).

 A Security Center az [Azure portálról](https://azure.microsoft.com/features/azure-portal/) érhető el. További információt a [portál dokumentációjában](https://azure.microsoft.com/documentation/services/azure-portal/) talál.


## A Security Center elérése

Hajtsa végre az alábbi lépéseket a portálon a Security Center eléréséhez:

1. Válassza a **Tallózás** lehetőséget, majd görgessen a **Security Center** elemhez.
![Az Azure Security Center elérése a portálon][1]

2. Válassza a **Security Center** lehetőséget. Ekkor megnyílik a **Security Center** panel.
3. Megkönnyítheti a **Security Center** panel elérését, ha jobbra fent a **Panel rögzítése az irányítópulton** lehetőséget választja.
![A „Panel rögzítése az irányítópulton” lehetőség][2]

## A Security Center használata

Biztonsági szabályzatokat állíthat be Azure-előfizetéseihez és -erőforráscsoportjaihoz. **Szabályzatot** az alábbi lépéseket követve állíthat be előfizetéséhez:

1. Válassza a **Szabályzat** csempét a **Security Center** panelen.
![Security Center][3]

2. A **Biztonsági szabályzat – Szabályzat definiálása előfizetésenként vagy erőforráscsoportonként** panelen válasszon ki egy előfizetést.
![A Biztonsági szabályzat panel az Azure Security Centerben][4]

3. A **Biztonsági szabályzat** panelen kapcsolja be az **Adatgyűjtés** beállítást az automatikus naplógyűjtéshez. Az **Adatgyűjtés** bekapcsolásával a monitorozási bővítményt az előfizetés összes jelenlegi és új virtuális gépére vonatkozóan üzembe helyezi.
4. Válassza a **Válasszon tárfiókot régiónként** lehetőséget. Minden olyan régió esetében, amelyben virtuális gépeket működtet, meg kell adnia az adott régió virtuális gépeiről gyűjtött adatok tárolására szolgáló tárfiókot. Ha nem ad meg tárfiókot az egyes régiókhoz, a rendszer automatikusan létrehozza azt. A gyűjtött adatokat biztonsági okokból más ügyfelek adataitól logikailag elkülönítve tároljuk.

     > [AZURE.NOTE] Azt javasoljuk, hogy kapcsolja be az adatgyűjtést, és először az előfizetések szintjén válasszon tárfiókot.  A biztonsági szabályzatok az Azure-előfizetések és -erőforráscsoportok szintjén állíthatók be, az adatgyűjtés és a tárfiók viszont csak az előfizetések szintjén konfigurálható.

5. Kapcsolja be azokat a **javaslatokat**, amelyeket a biztonsági szabályzat részeként szeretne megjeleníteni. Példák:

 - A **Rendszerfrissítések** bekapcsolása esetén a rendszer az összes támogatott virtuális gépen ellenőrzi, hogy telepítve vannak-e az operációs rendszer frissítései.
 - Az **Alapkonfigurációs szabályok** bekapcsolása esetén a rendszer az összes támogatott virtuális gépen ellenőrzi, hogy találhatók-e olyan operációsrendszer-beállítások, amelyek a virtuális gépet sebezhetőbbé tehetik a támadásokkal szemben.

A **javaslatok** megtekintése:

1. Lépjen vissza a **Security Center** panelhez, és válassza a **Javaslatok** csempét. A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. Ha a rendszer potenciális biztonsági hiányosságokat azonosít, akkor itt megjelenik egy javaslat.
2.  Válassza ki a kívánt javaslatot a további részletek megjelenítéséhez, illetve a probléma megoldásához.
![Javaslatok az Azure Security Centerben][5]

Az erőforrások állapotának és biztonsági állapotának megtekintése az **Erőforrás biztonsági állapota** panelen:

1.  Lépjen vissza a **Security Center** panelhez.
2.  Az **Erőforrás biztonsági állapota** csempe tartalmazza a **Virtuális gépek**, a **Hálózat**, az **SQL** és az **Alkalmazások** biztonsági állapotával kapcsolatos mutatókat.
3.  Válassza a **Virtuális gépek** lehetőséget a további információk megjelenítéséhez.
4.  A **Virtuális gépek** panelen látható állapotösszegzés a kártevőirtó-programok, rendszerfrissítések és újraindítások állapotát, valamint a virtuális gépre vonatkozó alapkonfigurációs szabályokat tartalmazza.
5.  Jelöljön ki a kívánt elemet a **VIRTUÁLIS GÉPEKKEL KAPCSOLATOS JAVASLATOK** területen a további információk megjelenítéséhez, illetve a szükséges beállítások konfigurálásához.
6.  Tekintse meg a kívánt virtuális gépekre vonatkozó további információkat.
![Az Erőforrás biztonsági állapota csempe az Azure Security Centerben][6]

A **biztonsági riasztások** megtekintése:

1.  Lépjen vissza a **Security Center** panelhez, és válassza a **Biztonsági riasztások** csempét. A **Biztonsági riasztások** panelen megjelenik a riasztások listája. A riasztásokat a Security Center biztonsági naplókra és hálózati tevékenységre vonatkozó elemzése alapján hozza létre a rendszer. A riasztások között az integrált partneri megoldások riasztásai is szerepelnek.
![Biztonsági riasztások az Azure Security Centerben][7]

2.  Jelölje ki a kívánt riasztást a további információk megjelenítéséhez.
![Biztonsági riasztások részletei az Azure Security Centerben][8]

**Partneri megoldások** állapotának megtekintése:

1. Lépjen vissza a **Security Center** panelhez. A **Partneri megoldások** csempén áttekintheti az Azure-előfizetésében integrált partneri megoldások biztonsági állapotát.
2. Válassza a **Partneri megoldások** csempét. Ekkor megnyílik egy panel, amely a Security Centerrel összekapcsolt partneri megoldások listáját tartalmazza.
![Partneri megoldások][9]

3. Válasszon ki egy partneri megoldást. Ebben a példában az **F5-WAF2** megoldást választjuk ki.  Ekkor megnyílik egy panel, amely az adott partneri megoldás és a megoldáshoz hozzárendelt erőforrások állapotát tartalmazza. A **Megoldáskonzol** kiválasztásával megnyílik az adott megoldáshoz tartozó partnerfelügyeleti felület.
![Partneri megoldás részletei][10]

## Következő lépések
Ez a dokumentum a Security Center biztonságfelügyeleti és szabályzatkezelési összetevőit ismertette. További információ:

- [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
- [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
- [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
- [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
- [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png



<!--HONumber=Jun16_HO2-->


