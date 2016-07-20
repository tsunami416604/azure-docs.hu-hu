<properties
   pageTitle="Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben | Microsoft Azure"
   description="Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="yurid"/>

# Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben
Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.

> [AZURE.NOTE] A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak.

## Mi az Azure Security Center?
 A Security Center a láthatóság növelésével segít a fenyegetések megelőzésében, észlelésében és kezelésében, és hozzájárul az Azure-erőforrások biztonságának felügyeletéhez. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

## Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és az integrált kártevőirtó programok és tűzfalak naplóadatait a valós fenyegetések észlelése és a vakriasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, amely az integrált partnermegoldások riasztásait is tartalmazza, ezenkívül a gyors vizsgálatra vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

A Microsoft biztonsági kutatói világszerte folyamatosan elemzik a megjelenő fenyegetéseket, beleértve a Microsoft fogyasztói, valamint vállalati termékei és online szolgáltatásai körében tapasztalható új támadási mintákat és trendeket. Ennek köszönhetően a Security Center az új biztonsági hiányosságok és rések észlelését követően frissíteni képes az észlelési algoritmusokat – így az ügyfelek lépést tudnak tartani az újabb és újabb fenyegetésekkel. Néhány azok közül a fenyegetéstípusok közül, amelyeket a Security Center képes észlelni:

- **Találgatásos típusú észlelés hálózati adatokon keresztül:** ez az észlelési mód a gépi tanulási modelljeit használja, és ezeken keresztül képes megérteni az alkalmazások hálózati forgalmának mintáit, így lehetővé teszi az illetéktelenül végrehajtott belépési kísérletek hatékonyabb észlelését
- **Találgatásos típusú észlelés végpontadatokon keresztül**: a gép naplóinak elemzésén alapul; lehetővé teszi a sikeres és a sikertelen kísérletek megkülönböztetését.
- **Kártékony IP-címekkel kommunikáló virtuális gépek:** a szolgáltatás összehasonlítja a hálózati forgalmat a Microsoft globális fenyegetésfelderítési adataival, felderíti a feltört gépeket, amelyek irányítókiszolgálókkal kommunikálnak (és fordítva).
- **Feltört virtuális gépek:** a gép naplóinak viselkedéselemzése és egyéb jelekkel való korreláció alapján a szolgáltatás azonosítja azokat a rendellenes eseményeket, amelyek valószínűleg a gép feltörésének és kihasználásának eredményei.

## Biztonsági riasztások kezelése

A **Biztonsági riasztások** csempén áttekintheti az aktuális riasztásokat. Az egyes riasztásokkal kapcsolatos további részletek megjelenítéséhez kövesse az alábbi lépéseket:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.

    ![Biztonsági riasztások csempe az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  A csempére kattintva nyissa meg a **Biztonsági riasztások** panelt, amely a lentiek szerint további információkat tartalmaz a riasztásról.

    ![Biztonsági riasztások panel az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

A panel alsó részén találhatók az egyes riasztások részletei. Rendezésükhöz kattintson arra az oszlopra, amely szerint rendezni szeretné a riasztásokat. Az oszlopok meghatározása:

- **Riasztás:** a riasztás rövid leírása.
- **Szám:** az adott típusú riasztások listája egy adott napra vonatkozóan.
- **Észlelte:** a riasztás kiváltásáért felelős szolgáltatás.
- **Dátum:** Az a dátum, amelyen az esemény történt.
- **Állapot:** A riasztás aktuális állapota. Háromféle állapot lehetséges:
    - **Aktív:** A biztonsági riasztást észlelték.
    - **Elvetve:** A felhasználó elvetette a biztonsági riasztást. Általában azoknak a riasztásoknak ez az állapota, amelyeket megvizsgáltak, és vagy megoldottak, vagy azok nem bizonyultak tényleges támadásnak.

- **Súlyosság:** A súlyosság szintje lehet magas, közepes vagy alacsony.

A riasztások dátum, állapot és súlyosság alapján szűrhetők. A riasztások szűrése olyan esetekben lehet hasznos, amikor szűkíteni kell a megjelenített biztonsági riasztások körét. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.

1. Kattintson a **Szűrő** lehetőségre a **Biztonsági riasztások** panelen. A **Szűrő** panel megnyílása után válassza ki azokat a dátumra, állapotra és súlyosságra vonatkozó értékeket, amelyeket meg kíván tekinteni.

    ![Riasztások szűrése az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2.  A biztonsági figyelmeztetés vizsgálatát követően kiderülhet, hogy az adott környezetre nézve a riasztás vakriasztás volt, vagy hogy egy bizonyos erőforrás esetén az észlelt viselkedés megegyezik a várt viselkedéssel. Ha úgy dönt, hogy egy biztonsági riasztás nem alkalmazandó, elvetheti és szűréssel eltüntetheti a riasztást a megjelenített riasztások közül. Egy biztonsági riasztást két módon lehet elvetni. Kattintson jobb gombbal a riasztásra, és válassza az **Elvetés** lehetőséget, vagy mozgassa az egérmutatót egy elemre, kattintson a jobb oldalon megjelenő három pontra, és válassza az **Elvetés** lehetőséget. A **Szűrő** lehetőségre kattintva és az **Elvetve** lehetőséget választva megtekintheti az elvetett biztonsági riasztásokat.

    ![Riasztások szűrése az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Válaszadás a biztonsági riasztásokra
Válasszon ki egy biztonsági riasztást, ha többet szeretne megtudni a riasztást kiváltó esemény(ek)ről, és arról, hogy milyen lépéseket kell tennie a támadás elhárítása érdekében. A biztonsági riasztások típus és dátum szerint vannak csoportosítva. Ha valamelyik biztonsági riasztásra kattint, megnyílik egy panel, amely a csoportosított riasztások listáját tartalmazza.

![Válaszadás a biztonsági riasztásokra az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

Ebben az esetben a riasztásokat gyanús RDP-tevékenység váltotta ki. Az első oszlopban a támadásnak kitett erőforrások jelennek meg, a másodikban az az időpont látható, amikor a rendszer észlelte a támadást, a harmadik a riasztás állapotát, a negyedik pedig a riasztás súlyosságát mutatja. Ezen információk áttekintése után kattintson a támadásnak kitett erőforrásra. Mint az alábbi példában is látható, ekkor megnyílik egy új panel, amely javaslatokat tartalmaz a biztonsági riasztással kapcsolatos teendőkre nézve.

![Javaslatok az Azure Security Centerben: mi a teendő a biztonsági riasztással kapcsolatban](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8-1.png)

A panel **Riasztás** mezőjében további információkat talál az eseményről. Ezek az információk áttekintést nyújtanak a biztonsági riasztás kiváltó okáról, a célerőforrásról, (adott esetben) a forrás IP-címéről, valamint a fenyegetés elhárítására vonatkozó javaslatokról.  Bizonyos esetekben a forrás IP-címe üres lesz (nem érhető el), mert a Windows nem minden biztonsági eseménynaplója tartalmazza az IP-címet.

> [AZURE.NOTE] A Security Center által javasolt elhárítási műveletek a biztonsági riasztástól függően különbözők lehetnek. Bizonyos esetekben előfordulhat, hogy az Azure egyéb funkcióit kell használnia a javasolt elhárítási művelet végrehajtásához. A példánkban a támadás elhárításának javasolt módja az, hogy a támadást végrehajtó IP-címet tiltólistára kell helyezni egy [hálózati hozzáférés-vezérlési lista](../virtual-network/virtual-networks-acl.md) vagy egy [hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md) típusú szabály használatával.


## Következő lépések
Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
- [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
- [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelési funkcióiról.



<!--HONumber=Jun16_HO2-->


