---
title: Oktatóanyag – Fenntartott példányok költségeinek optimalizálása az Azure Cloudynnel | Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan optimalizálhatja a fenntartott példányok költségeit az Azure-ban és az Amazon Web Servicesben (AWS).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: c50259f0df955c3a22edc979dfebc8bfb2059e16
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987766"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Oktatóanyag: Fenntartott példányok optimalizálása

Ebben az oktatóanyagban megismerheti, hogyan segít a Cloudyn optimalizálni a fenntartott példányok költségeit és kihasználtságát az Azure-ban és az Amazon Web Servicesben (AWS). A felhőszolgáltatók által biztosított fenntartott példányok hosszú távú szerződés keretében érhetőek el, amelyben a felhasználó előre elkötelezi magát a virtuális gép későbbi használata mellett. Ez a modell jelentős megtakarítást eredményezhet a virtuális gépek hagyományos használatalapú díjszabási modelljével szemben. Azonban csak akkor érhető el megtakarítás, ha teljes mértékben kihasználjuk a fenntartott példányok kapacitását.

Ez az oktatóanyag azt ismerteti, hogyan támogatja a Cloudyn az Azure-beli és AWS-beli fenntartott példányait (RI). Emellett leírja, hogyan optimalizálhatók a fenntartott példányok költségei. Elsősorban a foglalások teljes kihasználtságának biztosításával. Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure-beli fenntartott példányok költségeinek ismertetése
> * A fenntartott példányok előnyei
> * Az Azure-beli fenntartott példányok költségeinek optimalizálása
> * A fenntartott példányok költségeinek megtekintése
> * Az Azure-beli fenntartott példányok költséghatékonyságának felmérése
> * Az AWS-beli fenntartott példányok költségeinek optimalizálása
> * Ajánlott fenntartott példányok vásárlása
> * Nem használt foglalások módosítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-fiókkal.
- Rendelkeznie kell a Cloudyn próbaverziójával vagy fizetett előfizetésével.
- Rendelkeznie kell megvásárolt fenntartott példányokkal az Azure-ban vagy az AWS-ben.

## <a name="understand-azure-ri-costs"></a>Az Azure-beli fenntartott példányok költségeinek ismertetése

Azure Reserved VM Instances vásárlásakor az ügyfél előzetesen fizet a későbbi használatért. Az előzetes fizetés fedezi a virtuális gépek későbbi használatának költségeit:

- Egy adott típusú virtuális gépre vonatkozóan;
- Egy adott régióban;
- Egy vagy három évre;
- Adott mennyiségű megvásárolt virtuális gépre vonatkozóan.

A megvásárolt Azure Reserved VM Instances az Azure Portal [Foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) részén tekinthető meg.

Az _Azure-beli fenntartott VM-példány_ kifejezés csak egy díjszabási modellt jelent. A futó virtuális gépeket semmilyen módon nem változtatja meg. A kifejezés csak az Azure-ra érvényes, és általánosabban _fenntartott példány_ vagy _foglalás_ néven is ismert. A megvásárolt fenntartott példányok nem megadott virtuális gépekre érvényesek, hanem bármilyen megfelelő virtuális gépre. Például nézzünk egy foglalást, amely egy adott típusú virtuális gépre vonatkozik egy kiválasztott régióban.

A megvásárolt fenntartott példány csak az alaphardverre vonatkozik, a virtuális gépek szoftverlicenceire nem. Például lefoglalhat egy példányt, és rendelkezhet egy annak megfelelő, Windows rendszerű virtuális géppel. A fenntartott példány csak a virtuális gép alapköltségeit fedezi. Ebben a példában az esetlegesen szükséges Windows-licenc teljes árát az ügyfél fizeti. Ha a virtuális gépein futó operációs rendszerre vagy más szoftverekre is kedvezményt szeretne kapni, fontolja meg az [Azure Hybrid Benefits](https://azure.microsoft.com/pricing/hybrid-benefit) használatát. A Hybrid Benefits hasonló kedvezményeket nyújt a szoftverlicencekre, mint a fenntartott példányok az alap virtuális gépekre.

A fenntartott példányok használatának mértéke nem befolyásolja közvetlenül a költségeket. Ez azt jelenti, hogy a virtuális gép futtatása 100%-os és 0%-os CPU-kihasználtság esetén is ugyanazzal a költséggel jár: az előre fizetés a virtuális gép lefoglalására érvényes, nem a tényleges használatára.

A következő kép alapján tekintsük át a hagyományos, igény szerinti virtuálisgép-használat költségeit a fenntartott példányok költségeihez képest:

![Az igény szerinti használat költségeinek és a fenntartott példányok költségeinek összehasonlítása](./media/tutorial-optimize-reserved-instances/azure01.png)



A piros színű oszlopok jelzik a fenntartott példány megvásárlásának összesített költségét. Csak az egyszeri díjat kell kifizetni. A virtuális gép használata ingyenes. A kék sáv jelzi ugyanazon virtuális gép futtatásának összesített költségeit a használatalapú vagy igény szerinti díjszabási modell esetén. A virtuális gép használatának hetedik és nyolcadik hónapja között éri el a *megtérülési pontot*. Ebben a példában a nyolcadik hónaptól kezdve már pénzt takaríthat meg.

## <a name="benefits-of-ris"></a>A fenntartott példányok előnyei

Minden fenntartott példány megvásárlása egy megadott méretű és helyen található virtuális gépre vonatkozik. Például a következő képen az USA nyugati régiójában lévő D2s\_v3 látható:

![Az Azure-beli fenntartott példányok részletei](./media/tutorial-optimize-reserved-instances/azure02.png)

A fenntartott példány megvásárlása akkor előnyös, ha a virtuális gép elegendő ideig fut ahhoz, hogy elérje a foglalás megtérülési pontját. A virtuális gépnek meg kell felelnie a fenntartott példány méretének és helyének. Például a fenti ábrán a megtérülési pont körülbelül hét és fél hónapnál található. A vásárlás tehát akkor éri meg, ha a foglalásnak megfelelő virtuális gép futtatásának ideje legalább 7,5 hónap \* 30 nap \* 24 óra = 5400 óra. Ha a megfelelő virtuális gép 5400 óránál kevesebbet fut, akkor a foglalás drágább, mint a használatalapú fizetés.

A megtérülési pont a különféle virtuálisgép-méretek és helyek esetében eltérő lehet. Emellett függ az egyeztetett használatalapú ártól is. Mielőtt a vásárlás mellett dönt, ellenőrizze, hogy az Ön esetében hol van a megtérülési pont.

Egy másik megfontolandó szempont a foglalás megvásárlásakor a fenntartott példány hatóköre. A hatókör határozza meg, hogy a foglalás előnye megosztott-e, vagy csak egy adott előfizetésre vonatkozik. A megosztott fenntartott példányok véletlenszerűen lesznek kiosztva az összes előfizetésben az elsőként megtalált megfelelő virtuális gépekre.

A megosztott vásárlási hatókör a legrugalmasabb, ezért ennek a használata ajánlott, ha lehetséges. A megosztott hatókörrel jelentősen nő az összes fenntartott példány kihasználásának esélye. Azonban ha az előfizetés tulajdonosa a fenntartott példányért fizet, lehet, hogy nincs más lehetősége, mint hogy a vásárláskor az egyetlen előfizetéses hatókört válassza.

## <a name="optimize-azure-ri-costs"></a>Az Azure-beli fenntartott példányok költségeinek optimalizálása

A Cloudyn a következőképp támogatja a fenntartott példányok és a Hybrid Benefits használatát:

- Az egyes díjszabási modellekre vonatkozó költségek bemutatása
- A fenntartott példányok használatának nyomon követése
- A fenntartott példányok hatásának felmérése
- A fenntartott példányok költségeinek kiosztása a házirendek alapján

Fenntartott példányok megvásárlása előtt az első teendő felmérni, hogy a fenntartott példány megvásárlásának milyen hatása lesz:

- Mennyibe kerül?
- Mennyi pénzt lehet vele megtakarítani?
- Hol a megtérülési pont?

A fenntartott példányok vásárlásának hatását ismertető jelentés segíthet megválaszolni ezeket a kérdéseket.

## <a name="assess-azure-ri-cost-effectiveness"></a>Az Azure-beli fenntartott példányok költséghatékonyságának felmérése

A Cloudyn portálon lépjen az **Optimizer** > **RI Comparison** (Optimalizáló > Fenntartott példányok összehasonlítása) részre, majd válassza a **Reserved Instance Purchase Impact** (Fenntartott példányok vásárlásának hatása) lehetőséget.

A fenntartott példányok vásárlásának hatását ismertető jelentésben válassza ki a virtuális gép méretét (a példánytípust), a helyet (régiót), a foglalás időtartamát, a mennyiséget és a várt futásidőt. Ezek alapján felmérheti, hogy a vásárlás megtakarítást jelentene-e.

Például ha vásárol egy foglalást egy DS1\_v2 típusú virtuális gépre az USA keleti régiójában, amely az év minden napján, folyamatosan fut, akkor évente 369,48 dollár megtakarítást érhet el. A megtérülési pont öt hónap után jön el. Lásd a következő képet:

![Azure-beli fenntartott példány megtérülési pontja](./media/tutorial-optimize-reserved-instances/azure03.png)

Ha azonban csak az idő 50%-ában fut, akkor a megtérülési pont 10 hónap után jön el, az éves megtakarítás pedig mindössze 49,74 dollár lesz. Előfordulhat, hogy a példában szereplő példánytípus esetében nem tudna megtakarítást elérni a foglalás megvásárlásával. Lásd a következő képet:

![Megtérülési pont az Azure-ban](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>A fenntartott példányok költségeinek megtekintése

Egy foglalás megvásárlása egyszeri fizetéssel jár. A fizetés a Cloudyn szolgáltatásban kétféleképpen tekinthető meg:

- Tényleges költség
- Amortizált költség

### <a name="actual-reserved-instance-cost"></a>Fenntartott példányok tényleges költségei

A tényleges költségek elemzése és az elemzés egy adott időszakban jelentések a foglalásért fizetett teljes összeget mutatják a vásárlás hónapjától kezdve. Ezáltal segítenek nyomon követni a tényleges kiadásokat egy adott időszakban.

A Cloudyn portálon lépjen a **Cost** > **Cost Analysis** (Költségek > Költségek elemzése) részre, majd válassza az **Actual Cost Analysis** (Tényleges költségek elemzése) vagy az **Actual Cost Over Time** (Tényleges költségek időbeli alakulása) lehetőséget. Állítsa be a szűrőket. Például szűrhet az Azure/VM szolgáltatásra, és csoportosíthatja az eredményeket erőforrástípus és díjszabási modell szerint. Lásd a következő képet:

![Fenntartott példányok tényleges költségei](./media/tutorial-optimize-reserved-instances/azure05.png)

Szűrhet egy adott szolgáltatásra (jelen példában az **Azure/VM**-re), és csoportosíthatja az eredményeket **díjszabási modell** és **erőforrástípus** szerint, ahogyan az a következő képen látható:

![Tényleges költségeket bemutató jelentés csoportjai és szűrői](./media/tutorial-optimize-reserved-instances/azure06.png)

Elemezni lehet a korábbi kifizetések típusait is, például egyszeri díjak, használati díjak és licencdíjak.

### <a name="amortized-reserved-instance-cost"></a>Fenntartott példányok amortizált költségei

Fenntartott példányok vásárlásakor előre ki kell fizetni egy megadott díjat, amely a vásárlás hónapjánál jelenik meg. Ez a későbbi számlákban nem látható. Emiatt ha kizárólag a havi használatot veszi alapul, az félrevezető lehet. A tényleges havi költség a havi használati költségből és a korábbi egyszeri díjak időarányos részéből tevődik össze. Az amortizált költségek jelentése segít a költségek áttekintésében.

A fenntartott példányok amortizált költségének kiszámításához a rendszer a foglalás egyszeri díját veszi alapul, és amortizálja azt a foglalás időtartamára vonatkozóan. A tényleges költségek jelentéseiben az egyszeri díjak a foglalás megvásárlásának hónapjánál jelennek meg. A napi és a havi kiadások nem jelennek meg az üzemelő példány tényleges költségeiben. Az amortizált költségek jelentései az üzemelő példány tényleges költségeit mutatják meg egy adott időszakra vonatkozóan.  A tényleges költségek trendjei csak az amortizált költségek jelentésében tekinthetők át. Emellett ez az egyetlen módja a jövőbeli kiadások előrejelzésének is.

A tényleges költségek jelentésében egy 747 dollár értékű kiugrás látható november 16-hoz tartozóan egy fenntartott példány megvásárlása miatt. Az amortizált költségek jelentésében (lásd az alábbi képen) november 16-hoz egy részleges napi díj szerepel. November 17-től kezdve a fenntartott példány amortizált költsége látható, ami 747 dollár/365 = 2,05 dollár. Emellett az is látható, hogy a megvásárolt foglalás nincs használva, így az optimalizálásához érdemes lehet átváltani egy másik virtuálisgép-méretre.

A megtekintéshez lépjen a **Cost** > **Cost Analysis** (Költségek > Költségek elemzése) részre, majd válassza ki az **Amortized Cost Analysis** (Amortizált költségek elemzése) vagy az **Amortized Cost Over Time** (Amortizált költségek időbeli alakulása) lehetőséget.

![Fenntartott példányok amortizált költségei](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Az AWS-beli fenntartott példányok költségeinek optimalizálása

A fenntartott példányok megvásárlása nyílt kötelezettségvállalás. Akkor érdemes alkalmazni ezt a megoldást, ha a virtuális gépek tartósan használatban vannak, mivel a fenntartott példányok olcsóbbak, mint az igény szerinti példányok. Azonban a kihasználtságnak megfelelő mértékűnek kell lennie. A kötelezettségvállalás az erőforrások (általában virtuális gépek) használatára vonatkozik egy meghatározott ideig: egy vagy három évig. Ha úgy dönt, hogy vásárol, azzal előre kifizeti az erőforrásokat foglalás útján. Azonban előfordulhat, hogy nem mindig használja ki teljesen a lefoglalt erőforrásokat.

Tegyük fel, hogy felméri a környezetét, és megállapítja, hogy az elmúlt évben 20 standard D2 példánya futott folyamatosan. Ezekhez vásárolhat egy foglalást, és jelentős összegeket takaríthat meg. Egy másik példa: feltételezzük, hogy kötelezettségvállalást tett tíz MA4 példány használatára egy évre. Azonban eddig csak ötöt használt ki ezek közül. Mindkét példa a fenntartott példányok nem megfelelő kihasználását illusztrálja. A fenntartott példányok költségei kétféleképpen optimalizálhatók a Cloudyn optimalizálási jelentései segítségével:

- A használati előzményeken alapuló vásárlási javaslatok áttekintése
- Nem használt foglalások módosítása

Az _EC2 fenntartott példány vásárlására vonatkozó javaslatok_ és az _EC2 jelenleg nem használt foglalások_ jelentése alapján javíthatja a fenntartott példányai kihasználtságát és költségeit.

## <a name="buy-recommended-ris"></a>Ajánlott fenntartott példányok vásárlása

A Cloudyn összehasonlítja az igény szerinti példányok használatát és összeveti azt a fenntartott példányok használatának lehetőségével. Ha megtakarítási lehetőségeket észlel, megjeleníti a javaslatokat az EC2 vásárlási javaslatok jelentésében.

A portál tetején, a jelentések menüjében kattintson az **Optimizer** > **Pricing Optimization** > **EC2 RI Buying Recommendations** (Optimalizáló > Díjszabás optimalizálása > EC2 fenntartott példány vásárlására vonatkozó javaslatok) lehetőségre.

A következő képen vásárlási javaslatok láthatók a jelentésből.

![Vásárlási javaslatok](./media/tutorial-optimize-reserved-instances/aws01.png)

Ebben a példában a Cloudyn\_A fiókhoz 32, fenntartott példány vásárlására vonatkozó javaslat érhető el. Ha mindegyik vásárlási javaslatot követi, azzal évente akár 137 770 dollár megtakarítást érhet el. Vegye figyelembe, hogy a Cloudyn által megadott vásárlási javaslatok azt feltételezik, hogy a számítási feladatok futtatása egyenletes.

Az egyes javaslatok részletes indoklásának megtekintéséhez kattintson a pluszjelre ( **+** ) a **Justifications** (Indoklások) oszlopban. Íme egy példa a lista első javaslatára.

![Vásárlások indoklása](./media/tutorial-optimize-reserved-instances/aws02.png)

Az előbbi példa azt mutatja, hogy a számítási feladatok igény szerinti futtatása évente 90 456 dollárba kerülne. Ha azonban előre megvásárolja a foglalást, ugyanezen számítási feladatok végrehajtása 56 592 dollárba kerülne, vagyis évente 33 864 dollár lenne a megtakarítás.

Kattintson a pluszjelre az **EC2 fenntartott példány vásárlásának hatása** mellett, hogy megtekinthesse a megtérülési pontot az évben, vagyis azt, hogy a vásárlási befektetés mikor térül meg. A következő példában nagyjából nyolc hónappal a vásárlást követően az igény szerinti használat költségei túllépik a fenntartott példány összesített költségeit:

![Vásárlás hatása](./media/tutorial-optimize-reserved-instances/aws03.png)

Innentől kezdve Ön pénzt takarít meg.

A **Példányok egy adott időszakban** jelentésben ellenőrizheti a vásárlási javaslat pontosságát. Ebben a példában azt láthatja, hogy az elmúlt 30 napban a számítási feladatokhoz átlagosan hat példány lett felhasználva.

![Példányok egy adott időszakban](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Nem használt foglalások módosítása

A nem használt foglalások gyakoriak a felhőerőforrás-felhasználók számítási környezeteiben. A nem használt foglalások teljesen kihasználttá tételével pénzt takaríthat meg, ha módosítja a foglalásait az aktuális igényeinek megfelelően. Például tegyük fel, hogy az előfizetése standard D3 példányokat futtat Linuxon. Ha nem használja ki teljesen a foglalást, módosíthatja a példánytípust. Vagy áthelyezheti a nem használt erőforrásokat egy másik foglalásba vagy másik fiókba.

Az AWS által értékesített fenntartott példányok adott rendelkezésre állási zónákhoz és régiókhoz tartoznak. Ha egy adott rendelkezésre állási zónához tartozó fenntartott példányokat vásárolt, azokat nem helyezheti át más zónába. Azonban az egyes régiókhoz tartozó fenntartott példányokat egyszerűen áthelyezheti egyik zónából a másikba az **EC2 jelenleg nem használt foglalások** jelentése segítségével. Másik megoldásként módosíthatja a példányokat regionális hatókörűre, így minden rendelkezésre állási zónában megfelelő példányok lesznek alkalmazva.

A portál tetején, a jelentések menüjében kattintson az **Optimizer** > **Inefficiencies** > **EC2 Currently Unused Reservations** (Optimalizáló > Hatékonysági hiányosságok > Jelenleg nem használt foglalások) lehetőségre.

A következő képen a nem használt fenntartott példányok jelentése látható.

![Nem használt foglalások](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Kattintson a pluszjelre egy adott foglalás **Details** (Részletek) oszlopában a foglalás részleteinek megtekintéséhez.

![Nem használt foglalások részletei](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

Az előző példában összesen 77 nem használt foglalás található a különböző rendelkezésre állási zónákban. Az első foglalásban 51 nem használt példány található. A lista további részében a fenntartott példányok potenciális módosítási lehetőségei láthatók, amelyeknek elvégezhetők az **m3.2xlarge** példánytípus használatával a **us-east-1c** rendelkezésre állási zónában.

Kattintson a lista első foglalásánál a **Modify** (Módosítás) lehetőségre a **Modify RI** (Fenntartott példány módosítása) oldal megnyitásához, ahol a foglalás részletei láthatók.

![Fenntartott példány módosítása](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

Itt vannak felsorolva a módosítható fenntartott példányok. A következő képen például 51 nem használt foglalás látható, amelyek módosíthatók, de a két foglalásnak összesen 54 példányra van szüksége. Ha módosítja a nem használt foglalásokat, hogy az összes ki legyen használva, akkor a továbbiakban négy példány igény szerint lesz futtatva. Ebben a példában fel kell osztani a nem használt foglalásokat, hogy az első foglalás 30 példányt, a második pedig 21-et használjon.

Kattintson az első foglalási bejegyzéshez tartozó pluszjelre, majd állítsa a **Reservation quantity** (Foglalás mennyisége) értékét **30**-ra. A második bejegyzésnél állítsa a foglalás mennyiségét **21**-re, majd kattintson az **Apply** (Alkalmaz) lehetőségre.

![Foglalás mennyiségének módosítása](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

A foglalás összes nem használt példánya teljes mértékben ki van használva, és 51 példány már nem igény szerint fut. Ebben a példában a vállalkozás pénzt takaríthat meg az igény szerinti használat jelentős csökkentésével és a már kifizetett foglalások kihasználásával.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> * Az Azure-beli fenntartott példányok költségeinek megismerése
> * A fenntartott példányok előnyeinek megismerése
> * Az Azure-beli fenntartott példányok költségeinek optimalizálása
> * A fenntartott példányok költségeinek megtekintése
> * Az Azure-beli fenntartott példányok költséghatékonyságának felmérése
> * Az AWS-beli fenntartott példányok költségeinek optimalizálása
> * Ajánlott fenntartott példányok vásárlása
> * Nem használt foglalások módosítása


Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan szabályozható az adatokhoz való hozzáférés.

> [!div class="nextstepaction"]
> [Az adatok hozzáférésének szabályozása](tutorial-user-access.md)
