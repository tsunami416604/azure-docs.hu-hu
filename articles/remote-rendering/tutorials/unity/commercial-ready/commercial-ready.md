---
title: Forgalmazásra kész Azure Remote Rendering-alkalmazás létrehozása
description: A kereskedelmi használatra kész alkalmazások Azure Remote rendering használatával történő létrehozásának stratégiái és szempontjai
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0dad78ad76a870ea9f1db28a3cb5ccace5cd804f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510929"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Oktatóanyag: kereskedelmi használatra kész Azure Remote rendering-alkalmazás létrehozása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Munkamenet-kezelés kereskedelmi alkalmazásokhoz
> * Számlázási munkamenetek nyomon követése
> * A felhasználói élmény optimalizálása a munkamenet betöltési ideje alatt
> * A hálózati késéssel kapcsolatos megfontolások

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag a következő [oktatóanyagra épül: az Azure távoli renderelés és a Model Storage védelme](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>A kereskedelmi készültség bemutatása

Az Azure Remote rendering kibővíti a vegyes valósággal kapcsolatos lehetőségeket. Ha az alapok integrálva vannak a megoldásba, számos további szempontot is figyelembe kell venni, hogy a megoldás biztonságos, skálázható, és készen álljon a szolgáltatás értékének biztosítására.

Ez a modul bemutatja, hogy milyen további funkciókra lehet szükség a kereskedelmi alkalmazáshoz.

A rendszerszintű architektúrával kapcsolatos ajánlott eljárások széles körű áttekintését a következő webhelyen találja:

* [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/)
* [Első lépések útmutató Azure-fejlesztőknek](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)

## <a name="analytics"></a>Elemzés

Az elemzési eszközök integrálása segíthet a megoldás kezelésében, nyomon követésében és tökéletesítésében.

Az elérhető elemzési erőforrások átfogó listáját a következő webhelyen találja:

* [Az Azure elemzési szolgáltatásai](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>A számlázás használatának nyomon követése

Az Azure távoli renderelés több belső csapattal vagy külső ügyféllel való használatának nyomon követése fontos szempont, különösen a több-bérlős helyzetekben.

Ennek eléréséhez az Azure egy erőforrás-címkézés nevű szolgáltatást kínál, amely az Azure Remote rendering szolgáltatás felhasználását társítja az egyes ügyfelekkel.

Az erőforrás-elnevezéssel és a címkézéssel kapcsolatos további információkért érdemes elindulni:

* [Erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

### <a name="diagnostics"></a>Diagnosztika

Az olyan hatékony eszközök, mint a Windows esemény-nyomkövetés (ETW) és az esemény-nyomkövetési naplózás (ETL) megkönnyítik az alkalmazáson belüli nyomkövetési események létrehozását, és segíthetnek a hálózat, a tartalom betöltésének, a munkamenet, az alkalmazás és más, a kereskedelmi megoldások üzembe helyezése során felmerülő problémák diagnosztizálásában.

További információért látogasson el ide:

* [Ügyféloldali teljesítmény-nyomkövetés létrehozása](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)
* [Windows esemény-nyomkövetés-(ETW-) adatok összegyűjtése](https://docs.microsoft.com/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [A Windows-eszközök portáljának használata: naplózás](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Használat elemzése

Az Azure Application Insights segítségével megismerheti, hogy a felhasználók miként használják az Azure-beli távoli renderelési alkalmazást. Minden alkalommal, amikor frissíti az alkalmazást, megvizsgálhatja, hogy milyen jól működik a felhasználók számára, és ennek megfelelően fokozza a megoldást. Ezzel az ismerettel a következő fejlesztési ciklusokra vonatkozó adatvezérelt döntéseket hozhat.

További információért látogasson el ide:

* [Használat elemzése Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

## <a name="fast-startup-time-strategies"></a>Gyors indítási idő stratégiák

Előfordulhat, hogy a használati eset gyors indítást igényel az alkalmazás elindítása és a 3D-modell megjelenítéséhez. Például egy olyan fontos értekezlet során, amelyben kritikus fontosságú, hogy minden időben megtörténjen a működés. Egy másik példa a CAD 3D-modell áttekintése során, ahol a CAD-alkalmazások és a vegyes valóságok közötti gyors tervezési iteráció kulcsfontosságú a hatékonyság érdekében.

Az Azure távoli rendereléshez előre feldolgozható 3D-modellek szükségesek, és az Azure jelenleg több percet vesz igénybe, és betölt egy modellt a rendereléshez. Ennek a folyamatnak a lehető legzökkenőmentesebb és gyors elvégzése a 3D-modell és az ARR-munkamenet előkészítését teszi szükségessé.

Az itt megosztott javaslatok jelenleg nem tartoznak a szabványos Azure-alapú távoli rendereléshez, de a gyorsabb indítási idő érdekében saját maguk is végrehajthatják azokat.

### <a name="initiate-early"></a>Korai indítás

Az indítási idő csökkentése érdekében a legegyszerűbb megoldás, ha a munkamenet létrehozását és inicializálását a lehető leghamarabb át szeretné helyezni a felhasználói munkafolyamatban. Az egyik stratégia a munkamenet inicializálása, amint ismert, hogy egy ARR-munkamenetre lesz szükség. Ez gyakran akkor történik meg, amikor a felhasználó megkezdi a 3D modell feltöltését az Azure-Blob Storage az Azure távoli rendereléssel való használatra. Ebben az esetben a munkamenet-létrehozást és az inicializálást a 3D-modell feltöltésével megegyező időben lehet kezdeményezni, hogy mindkét munkafolyamat párhuzamosan fusson.

Ez a folyamat tovább egyszerűsíthető annak biztosításával, hogy a kiválasztott Azure Blob Storage bemeneti és kimeneti tárolók ugyanabban a regionális adatközpontban legyenek, mint az Azure távoli renderelési munkamenet.

### <a name="scheduling"></a>Ütemezés

Ha tudja, hogy van egy jövőbeli Azure-renderelési igénye, akkor egy adott dátumot és időpontot ütemezhet az Azure távoli renderelési munkamenetének elindításához.

Ez a lehetőség egy webportálon keresztül érhető el, ahol a felhasználók egyszerre tölthetik fel a 3D-modelleket, és ütemezhetik az időt a későbbi megtekintéshez. Az is jó megoldás, ha más beállításokat is szeretne kérni, például a [*standard*](../../../reference/vm-sizes.md) vagy a [*prémium*](../../../reference/vm-sizes.md) megjelenítést. A *prémium* szintű renderelés akkor lehet megfelelő, ha olyan eszközöket szeretne megjeleníteni, amelyekben az ideális méretet nehezebb automatikusan meghatározni, vagy annak biztosítására, hogy az Azure-régió a megadott időpontban elérhető virtuális gépekkel rendelkezik.

### <a name="session-pooling"></a>Munkamenet-készletezés

A legigényesebb helyzetekben egy másik lehetőség a munkamenet-készletezés, ahol egy vagy több munkamenet létrehozása és inicializálása mindig folyamatban van. Ez egy munkamenet-készletet hoz létre, amely azonnali használatra kéri a kérelmező felhasználó számára. Ennek a megközelítésnek a hátránya, hogy a virtuális gép inicializálása után a szolgáltatás számlázása elindul. Nem költséghatékony, hogy egy munkamenet-készletet folyamatosan futtasson, de az elemzések alapján lehetséges, hogy megjósolhatja a maximális terhelést, vagy kombinálható a fenti ütemezési stratégiával, hogy előre megjósolja a munkamenetek szükségességét, és ennek megfelelően felgyorsítja a munkamenet-készletet.

Ez a stratégia a *standard* és a *prémium* szintű munkamenetek közötti választást is lehetővé teszi a dinamikus módon, mert sokkal gyorsabban lehet váltani a két típus között egy adott felhasználói munkamenetben, például a *prémium* komplexitású modell első megtekintésekor, majd egy, a *standardon*belül működhet. Ha ezek a felhasználói munkamenetek meglehetősen hosszadalmasak, jelentős költségmegtakarítást érhet el.

További információ az Azure távoli renderelési munkamenetekről:

* [Remote Rendering-munkamenetek](https://docs.microsoft.com/azure/remote-rendering/concepts/sessions)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Standard és prémium szintű kiszolgálói útválasztási stratégiák

A standard vagy *prémium* *szintű* kiszolgáló méretének kiválasztásához a felhasználói élmény és a végpontok közötti rendszer megtervezése kihívást jelent. Habár a kizárólag *prémium* szintű munkamenetek használata egy lehetőség, a *normál* munkamenetek jóval kevesebb Azure számítási erőforrást használnak, és a *prémiumnál*olcsóbbak. Ez erős motivációt biztosít a *standard szintű* munkamenetek használatára, amikor csak lehetséges, és csak szükség esetén használja a *prémium* szintet.

Itt több lehetőséget is megosztunk, amelyek közül legalább a legátfogóbbat vesszük igénybe, hogy kezeljék a munkamenet-döntések kezelésének óhaját.

### <a name="use-only-standard-or-premium"></a>Csak standard vagy prémium szintű használata

Ha biztos abban, hogy az igényeinek *mindig* a *standard* és a *prémium*küszöbértéke alá esik, akkor ez jelentősen leegyszerűsíti a döntést. Csak a *standard*használata. Ne feledje azonban, hogy a felhasználói élményre gyakorolt hatás jelentős, ha a betöltött eszközök teljes összetettségét túl összetettnek tekinti a *normál* munkamenetek esetében.

Hasonlóképpen, ha a felhasználás nagy hányada meghaladja a *standard* és a *prémium*közötti küszöbértéket, vagy a költségek nem kulcsfontosságú tényezők a használati esetekben, akkor a *prémium* lehetőség kiválasztásával is megtarthatja az egyszerűséget.

### <a name="ask-the-user"></a>A felhasználó megkérdezése

Ha mind a standard, mind a *prémium* *szintű* támogatást szeretné támogatni, a legegyszerűbb módszer annak meghatározására, hogy milyen típusú munkamenetet kell létrehoznia, hogy megkérdezze a felhasználót, amikor kiválasztja a 3D-eszközöket a megtekintéshez. Ezzel a módszerrel a felhasználónak meg kell ismernie a 3D-eszköz összetettségét, vagy akár több, megtekinthető eszközt is. Ez általában nem ajánlott erre az okból. Ha a felhasználó helytelenül választ, és a *standard*lehetőséget választja, akkor az eredményül kapott felhasználói élmény egy alkalmatlan pillanatban sérült.

### <a name="analyze-the-3d-model"></a>A 3D modell elemzése

Egy másik viszonylag egyszerű módszer a kiválasztott 3D-eszközök bonyolultságának elemzése. Ha a modell összetettsége a *standard*küszöbérték alatt van, indítson el egy normál munkamenetet, ellenkező esetben egy *prémium* *szintű* munkamenetet kezdeményezzen. Itt az a kihívás, hogy egy-egy munkamenet végső soron több olyan modell megtekintésére is használható, amelynek némelyike meghaladhatja a *normál* munkamenet bonyolultsági küszöbértékét, ami azt eredményezi, hogy a különböző 3D-eszközök sorozatából nem képes zökkenőmentesen használni ugyanazt a munkamenetet.

### <a name="automatic-switching"></a>Automatikus váltás

A standard és a *prémium* *szintű* munkamenetek közötti automatikus váltás sok értelmet biztosít egy olyan rendszerkialakításban, amely magában foglalja a munkamenet-készletezést is. Ez a stratégia lehetővé teszi az erőforrás-használat további optimalizálását. Ahogy a felhasználó megtekinti a modelleket, meg kell határozni a bonyolultságot, és a munkamenetek megfelelő méretét kéri a rendszer a munkamenet-készletezési szolgáltatástól.

## <a name="working-with-networks"></a>Hálózatok használata

### <a name="diagnostics"></a>Diagnosztika

Az Azure-alapú távoli renderelés gyors internetkapcsolatot igényel, alacsony késéssel. A felhasználó hálózatának minősége jelentős hatással lehet a felhasználói élmény minőségére. Mivel az ügyfelek valószínűleg különböző hálózati konfigurációkkal rendelkeznek, és csak időnként gyenge hálózati késéssel rendelkeznek, a diagnosztikai eszközök kulcsfontosságúak.  

Annak érdekében, hogy következetesen magas színvonalú élményt nyújtson, javasoljuk, hogy a kiszolgálóoldali és az ügyféloldali elemzési eszközöket integrálja az Azure távoli renderelési alkalmazásaiba. Ezt az információt az ügyfelek által tapasztalt hálózati problémák diagnosztizálásához és enyhítéséhez szükséges információk alapján teheti meg.

### <a name="client-network-configurations"></a>Ügyfél-hálózati konfigurációk

A nagyvállalati környezetekben üzembe helyezett robusztus együttműködési megoldások fejlesztésének egyik legnagyobb kihívása a különböző hálózati topológia és az ügyfelek által esetlegesen használt vállalati tűzfal-konfigurációk előkészítése.

Számos vállalat blokkolja az összes társközi forgalmat egy helyi hálózaton belül. Ez megnehezíti az automatikus LAN-felderítés egyszerűségét és gördülékenyen kihasználva, hogy helyi megosztott munkamenetet hozzon létre a vegyes valóság alkalmazása összes felderített példánya között.

Más lehetséges meghibásodási pontok az útválasztók úgy vannak konfigurálva, hogy szándékosan szabályozzák a sávszélességet és a legtöbb TCP/IP-portot blokkoló tűzfalakat.

Ha egy ismeretlen hálózaton szeretné használni az Azure Remote rendering szolgáltatást, javasoljuk a következőket:

* A hálózati készültség értékeléséhez adjon meg egy előzetes találkozót ellenőrző feladatlistát.
* Győződjön meg arról, hogy a megfelelő regionális adatközpont képes a kérés kiszolgálására.
* Rengeteg időt kell kipróbálni a problémák diagnosztizálásához.
* Készítsen biztonsági mentést a nagy sávszélességű adatcsomaggal rendelkező mobil Hotspotról.

### <a name="end-to-end-bandwidth"></a>Végpontok közötti sávszélesség

Fontos felmérni a hálózat minden egyes szakaszának sávszélesség-funkcióit, amelyek az Azure távoli renderelési virtuális gép és a végfelhasználó között létezhetnek. Ne feledje, hogy az Azure-adatközpontból az ügyfél INTERNETSZOLGÁLTATÓjának hálózati szegmense több korlátozási tényező lehet, mint az ISP és az ügyfél között. A blob letöltési sebességének tesztelése segíthet az ilyen problémák diagnosztizálásában.

### <a name="bandwidth-competition"></a>Sávszélesség-verseny

A vegyes valóság alkalmazásának tervezésekor vegye figyelembe, hogy az alkalmazás különböző funkciói az Azure távoli renderelési szolgáltatásával is versenyeznek a sávszélességgel. A legnagyobb valószínűséggel nem várt példa az, hogy egy adott helyiség sok résztvevője egyszerre használja az ARR-t egy 3D-eszköz megtekintéséhez. A hálózati adatfolyam minden egyes szakaszának képesnek kell lennie az összes ARR adatfolyam összesített összegének továbbítására.

Egyéb példák: továbbított videó, egyidejű háttérbeli feltöltések más kapcsolódó tartalmakhoz és hangalapú csevegéshez, különösen, ha sok résztvevő van, és a rendszer a középső megközelítésű hangkeverő kiszolgálót használó, egymáshoz osztott társközi megközelítést használ.

A hálózati elemzéssel kapcsolatos további információkért tekintse meg a következőt:

* [Azure Storage Blob letöltési sebesség tesztelése](https://www.azurespeed.com/Azure/Download)
* [Az Azure Network Round Trip késési statisztikái](https://docs.microsoft.com/azure/networking/azure-network-latency)
* [Kiszolgálóoldali teljesítmény-nyomkövetés](https://docs.microsoft.com/azure/remote-rendering/overview/features/performance-queries)
* [Ügyféloldali teljesítmény-nyomkövetés](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)

## <a name="collaboration-considerations"></a>Együttműködési megfontolások

Az Azure Remote rendering egyik legértékesebb felhasználási területe a több résztvevő közötti együttműködés, amely ugyanazokat a 3D-élményt tekinti át. Ezekben a megosztott munkamenetekben fontos tisztában lenni azzal, hogy minden résztvevőnek egyedi Azure távoli renderelési munkamenetre van szüksége, függetlenül attól, hogy ugyanazon a helyen találhatók-e ugyanazon a hálózaton, vagy sem.

Ez azért igaz, mert mindegyik résztvevő ugyanazt a felhasználói élményt látja, mint a különböző nézőpontokból, amelyekhez ugyanazt a 3D-eszközt kell megadnia az egyes nézőpontokból.

### <a name="multiple-azure-remote-rendering-sessions"></a>Több Azure távoli renderelési munkamenet

Ha támogatni kívánja az Azure távoli rendereléssel kapcsolatos közös tapasztalatokat, az ARR-munkamenetek létrehozásához és kezeléséhez létrehozott rendszereknek elő kell készülniük a több munkamenet elindításához. Előfordulhat, hogy ezeket a munkameneteket különböző Azure-adatközpontokban kell inicializálni, ha a résztvevők földrajzilag elszórtan vannak.

A rendszernek emellett azt a lehetőséget is biztosítania kell, hogy egy vagy több résztvevő olyan földrajzi régióban legyen, amelyet jelenleg nem támogat az Azure távoli renderelés, vagy jelenleg nem rendelkezik elérhető Azure távoli renderelési virtuálisgép-példányokkal.

A több egyidejű munkamenet kezelése tovább egyszerűsíthető a munkamenet-készletezés és a jelen dokumentumban tárgyalt más stratégiák együttes használata esetén.

### <a name="azure-blob-storage-considerations"></a>Az Azure Blob Storage szempontjai

Minden egyidejű ARR-munkamenet hivatkozhat arra a SAS URI-ra, amelyre a konvertált modell megtekinthető. Ez lehetővé teszi, hogy egyszer töltse fel és alakítsa át a kívánt 3D-eszközöket, majd ossza meg az összes munkamenetben. Ez különösen akkor igaz, ha a résztvevők közösen találhatók, és ugyanazt az adatközpontot használják, ahol az Azure Blob Storage kapcsolatos teljesítménybeli problémák nem az Azure-beli távoli renderelési kiszolgálóval és a felhasználóval eltérő adatközpontban találhatók.

Ha a 3D-eszközöket általában egyetlen megtekintési munkamenetre töltötték fel, majd elveti, például a tervezési felülvizsgálati munkamenetben, az Azure-Blob Storage az Azure távoli renderelési kiszolgálóhoz viszonyítva a földrajzi régió is kevésbé kritikus.

Ha azonban a 3D-eszközök többször is felhasználhatók, például egy tanítási használati eset esetében, javasoljuk, hogy minden olyan regionális adatközpontban, ahol az Azure távoli renderelést szeretné használni, a blob Storage-ban készen áll a 3D-eszközök folyamatos tárolására. Ez automatizálható az Azure Storage-redundancia használatával. A CDN-t gyakran erre a célra használják, de ez még nem érhető el az Azure távoli rendereléshez.

További információk:

* [Vegyes valóságban megosztott tapasztalatok](https://docs.microsoft.com/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

## <a name="managing-model-access"></a>Modell-hozzáférés kezelése

Az Azure Remote rendering kihasználása teljes mértékben megköveteli a 3D modellek felügyeletéhez szükséges végpontok közötti infrastruktúra alapos vizsgálatát.

Az Azure Remote rendering használatának előnye, hogy a nagyméretű 3D-eszközöket soha nem kell közvetlenül a kevert valóság eszközre továbbítani a megtekintés előtt.  Továbbá, ha a 3D-eszközt feltöltik és átalakították az Azure Remote rendering használatával, tetszőleges számú felhasználó megoszthatja a 3D modell egyetlen példányát.

### <a name="considerations-for-3d-model-access"></a>A 3D-modellekhez való hozzáférés szempontjai

Íme néhány fontos szempont a modell-hozzáférési stratégiának való döntés során.

A várható használati eset alapján határozza meg a legjobb helyet vagy a helyek kombinációját, hogy a felhasználó kiválassza a 3D-eszközöket megtekintésre. Néhány gyakori lehetőség:

* Közvetlenül a vegyes valóság felületén belül
* A Companion webes portálon keresztül
* Egy társ asztali vagy mobil alkalmazásban

Ha a használati eset olyan használati mintázattal rendelkezik, ahol ugyanaz a 3D-eszköz többször is feltölthető, a háttér nyomon követheti, hogy mely modellek legyenek már konvertálva az ARR-vel való használatra, hogy a modell csak egyszer legyen feldolgozva több jövőbeli kiválasztáshoz. A tervezési áttekintés például az, hogy egy csapat Hogyan férhet hozzá egy közös eredeti 3D-eszközhöz. Minden csapattagnak a modelljét az ARR használatával kell áttekintenie a munkahelyi stream egy pontján. Ekkor csak az első nézet aktiválja az előfeldolgozás előtti lépést. A következő nézetek kikeresik a társított, feldolgozott, a SAS kimeneti tárolóban lévő fájlt.

A használati esettől függően valószínű, hogy meg kell határoznia, hogy megmaradjon a megfelelő Azure Remote rendering Server-méret, *standard* vagy *prémium*szint az egyes 3D-eszközök vagy-csoportok számára, amelyek ugyanabban a munkamenetben lesznek megtekintve.  

### <a name="on-device-model-selection-list"></a>Az eszközön a modell kiválasztási listája

Számos felhasználási esetben, például a képzés, a feladathoz kapcsolódó útmutatás vagy a marketing alkalmazás esetében az Azure-beli távoli renderelés során gyakran megtekinthető 3D-eszközök készlete meglehetősen statikus lehet. Ilyen helyzetekben a 3D-eszközök egy kiválasztott készlete előre átalakítható és elérhetővé válik egy adatbázison keresztül, amely tartalmazza a kurátori eszközök kiválasztási listájának feltöltéséhez szükséges információkat. Ezek az adatok lekérhető a vegyes valóság alkalmazásból a kiválasztási menü feltöltéséhez.

Ez egy lépéssel tovább is felhasználható privát 3D-eszközök feltöltésére, amelyek mindegyike egyedi vagy csoportos. A privát adategységek listája összekapcsolható a felhasználói élményben lévő, a 3D-eszközök megtekinthető kiválasztására szolgáló közös, megszerzett eszközök listájával.

### <a name="on-device-onedrive-access"></a>Eszközön OneDrive hozzáférés

Mivel a OneDrive file Picker natív módon van felépítve a Microsoft vegyes valóságú eszközeibe, a OneDrive lévő 3D-eszközök kiválasztásával vonzó, különösen olyan használati esetekhez, ahol gyakori a különböző vagy módosított 3D modellek betöltése. Ebben az esetben a felhasználó egy vagy több 3D-eszközt választ ki a vegyes valóság alkalmazásban található OneDrive fájl-választón keresztül. Ezt követően a rendszer áttelepíti a 3D-eszközöket egy SAS-bemeneti tárolóba, amelyet SAS kimeneti tárolóba konvertál, és az ARR-munkamenethez csatolva van. Ideális esetben a vegyes valóság alkalmazása felhőalapú folyamatot indít el ezen lépések elvégzéséhez, és nem helyezi át az összes bitet a OneDrive az eszközre, és vissza kell térnie az Azure Blob Storagera.

Ez a megközelítés még egy lépésből állhat azáltal, hogy megtartja a korábban megtekintett 3D-eszközök közötti társítást, amely azt eredményezi, hogy ha ugyanezt a modellt ismét kiválasztja a OneDrive-ből, az alkalmazás megkerülheti az átalakítási folyamatot, és közvetlenül betöltheti a társított konvertált 3D-eszközt SAS URI-ja segítségével.

További információk:

* [Microsoft Power automatizáló sablon az Azure Storage-replikáció OneDrive](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [A OneDrive File Storage API áttekintése](https://docs.microsoft.com/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Közvetlen CAD-hozzáférés

A vegyes valóság egyik meggyőző használati esete a CAD-munkafolyamatok tervezési felülvizsgálata. Ebben a forgatókönyvben a leggyorsabban betöltési idő az asztalról a kevert valóságra a legfontosabb. Az ideális megoldás lehet a plugins fejlesztése adott CAD-alkalmazásokhoz. Ezek a beépülő modulok közvetlenül a terhelés, a konvertálás és a megtekintés folyamatának minden aspektusát felügyelik:

* Adjon meg egy UX-et a következőhöz:
  * A CAD-alkalmazás párosítása egy adott vegyes valóságú eszközzel (egyszer).
  * Azt kéri, hogy a kiválasztott geometria a kevert valóság eszközön legyen megtekintve.
* Ha még nem fut, forgassa el az Azure távoli renderelési munkamenetét, hogy párhuzamosan tudja feldolgozni a CAD-fájl feltöltésekor és átalakításakor
* A CAD-geometriai adatok normalizálása az Azure távoli renderelés által támogatott formátumok egyikére
* A normalizált adatok közvetlen továbbítása az Azure Blob Storage bemeneti tárolóba
* A modell átalakítási folyamatának kezdeményezése
* A modell kimeneti tárolójának SAS URI-ja összekapcsolása az Azure távoli renderelési munkamenettel
* Értesítse a párosított vegyes valóságot, amely a modell elérhető, és készen áll arra, hogy megtekintse és megadja a kimeneti tároló SAS URI-JÁT, hogy az alkalmazás csatolja azt a munkamenethez.

A sokkal egyszerűbb, de valamivel kevésbé áramvonalas megközelítés automatizálhatja a 3D modell helyi merevlemezre mentésének folyamatát, majd kezdeményezheti a mentett fájlnak a SAS bemeneti tárolóba való továbbításának folyamatát.

### <a name="azure-marketplace"></a>Azure Piactér

Számos nagyvállalati ügyfél azt adja meg, hogy a Azure Stack a saját Azure-fiókjaik és a hitelesítő adataik biztonsági okokból történő üzembe helyezésére is szükség van. Ennek elvégzéséhez érdemes megfontolnia az Azure által felügyelt alkalmazások csomagolását úgy, hogy az Azure Marketplace-en is közzétehető legyen az Azure piactéren.

További információk:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Oktatóanyag: Azure által felügyelt alkalmazások közzététele a piactéren](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-marketplace-app)

### <a name="security"></a>Biztonság

Fontos, hogy az alapoktól kezdve a teljes körű Azure távoli renderelési megoldást hozza létre a biztonság érdekében. Számos biztonsági szempontot figyelembe kell venni a teljes körű megoldás kialakításában, beleértve a következőket:

* Hitelesítési stratégiák
* Hozzáférés-kezelés – csoportok, házirendek és engedélyek
* Több-bérlős üzemmód
* Adattárolás és adatátviteli titkosítás
* Ideiglenes használati tokenek
* Elosztott szolgáltatásmegtagadási (DDoS) támadások
* Fenyegetések észlelése
* VPN-EK és biztonságos hálózatok
* Tűzfalak
* Tanúsítvány-és titkos kulcsok kezelése
* Alkalmazás biztonsági rése és kiaknázása

A hitelesítéshez érdemes az ARR-hitelesítés és a munkamenet-felügyelet nagy részét az Azure-webszolgáltatásba helyezni. Ez hatékonyabban felügyelt és biztonságosabb megoldást eredményez.

További információk:

* [Azure AD-szolgáltatás hitelesítése](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-service-authentication)
* [A biztonsági helyzet megerősítése az Azure-ban](https://azure.microsoft.com/overview/security/)
* [Felhőbeli biztonság](https://azure.microsoft.com/product-categories/security/)
