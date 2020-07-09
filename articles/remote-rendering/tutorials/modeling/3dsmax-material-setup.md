---
title: Fizikai alapú renderelési anyagok beállítása a 3DSMax-ben
description: A cikk azt ismerteti, hogyan állíthatók be fizikailag alapú renderelési anyagok a 3DSMax-ben, és hogyan exportálhatók FBX formátumba.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85880100"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Oktatóanyag: fizikailag vezérelt renderelési anyagok beállítása a 3D Studio Max-ban

## <a name="overview"></a>Áttekintés
Az oktatóanyag segítségével megtanulhatja a következőket:

>[!div class="checklist"]
>
> * Az anyagokat speciális megvilágítás használatával rendelheti hozzá a jelenetben lévő objektumokhoz.
> * Objektumok és anyagok egypéldányos kezelése.
> * FBX-formátumba exportálhatja a jeleneteket, és kiválaszthatja a fontos beállítások lehetőséget.

A [fizikai alapú renderelési (pbr) anyagok](../../overview/features/pbr-materials.md) a 3D Studio Max (3DSMax) szolgáltatásban való létrehozása viszonylag egyszerű feladat. A PBR számos módon hasonló a más tartalom-létrehozási alkalmazásokhoz, például a Mayahoz. Ez az oktatóanyag az Azure-beli távoli renderelési projektek alapszintű PBR shader-telepítésének és FBX-exportálásának útmutatója.

Az oktatóanyagban szereplő minta jelenet számos sokszög típusú objektumot tartalmaz. Különböző anyagokat rendelnek hozzájuk, például fa, fém, festett fém, műanyag és gumi. Általánosságban elmondható, hogy minden anyag a következő textúrákat tartalmazza:

* **Albedó**, amely az anyag színtérképe, és más néven **diffúz** vagy **BaseColor**.
* Fémesség, amely meghatározza, hogy egy anyag fémes- **e, és**hogy mely részek fémesek. 
* **Érdesség**, amely meghatározza, hogy a felület milyen durva vagy zökkenőmentes legyen.
Emellett hatással van a reflexiók és a csúcsfények élességére vagy homályos is a felületen.
* **Normál**, amely részletes adatokat ad hozzá egy felülethez anélkül, hogy további sokszögeket kellene hozzáadnia. A részletek közé tartozhatnak például a fém felületen vagy a gabona faanyagban való kihúzása.
* **Környezeti elzáródás**, amely lágy árnyékolás hozzáadására és az árnyékok a modellhez való kapcsolódására szolgál. Ez egy szürkeárnyalatos Térkép, amely azt jelzi, hogy a modell mely területein kap teljes világítást (fehér) vagy teljes árnyalatot (fekete).

## <a name="prepare-the-scene"></a>A jelenet előkészítése
A **3D Studio Max**esetében a pbr-anyagok beállításának folyamata a következő.

A kezdéshez, ahogy látni fogjuk a minta jelenetben, számos Box objektumot hoztunk létre, amelyek mindegyike más típusú anyagokat képvisel:

>[!TIP]
>Érdemes megjegyezni, mielőtt megkezdi az olyan eszközök létrehozását, amelyekben a méréshez **mérőműszereket** használ.  
>Ezért javasoljuk, hogy állítsa be a jelenet **Rendszeregységeit** **méterre**. Emellett ajánlott, hogy az exportálás során a FBX exportálási beállításainál a mérőszámok egységei értékre legyenek beállítva.

Az alábbi ábrán a rendszeregységek a 3D Studio Max-ban való beállításának lépéseit mutatjuk be. A főmenüben **válassza az**  >  **egységek beállítása**  >  **rendszeregységek beállítása** lehetőséget, majd a **rendszeregységek méretezési** legördülő menüben válassza a **mérőműszerek**elemet. 
![rendszeregységek](media/3dsmax/system-units.jpg)

Ha a rendszeregységek méterre vannak beállítva, megkezdjük a modelljeink létrehozását. A minta jelenetben több Box-objektumot hozunk létre, amelyek mindegyike különböző típusú anyagokat képvisel – például fém-, gumi-, műanyag-és egyéb anyagok. 

>[!TIP]
>Hasznos lehet az olyan eszközök létrehozása, amelyeknek megfelelően kell eljárnia. Így könnyebben megtalálhatja a későbbiekben, ha a jelenet sok objektumot tartalmaz

![objektumok átnevezése](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Anyagok kiosztása

A jelenetben létrehozott egyes objektumok esetében – ebben az esetben több kocka is megkezdheti a PBR telepítését:

* A fő eszköztáron kattintson az anyag- **szerkesztő** ikonra az alábbi képen látható módon. Az **M** billentyűt is elérheti a billentyűzeten a szerkesztő megnyitásához. Az anyag-szerkesztőnek két módja van, amelyek kiválaszthatók a **módok** legördülő menüben – a **kompakt anyag szerkesztő** üzemmód és a **pala anyag** mód. Mivel ez a jelenet viszonylag egyszerű, a **kompakt módot**fogjuk használni.

* Az anyag-szerkesztőben számos gömb jelenik meg – ezek a gömbök az anyagok. Az alábbi anyagok egyikét fogjuk hozzárendelni minden egyes Object-Box-hez. A hozzárendelés elvégzéséhez először válassza ki az egyik objektumot a fő nézetablakban. Ha ezt a lehetőséget választja, kattintson az anyag-szerkesztő ablak első elemére. Miután hozzárendelte egy objektumhoz, a kiválasztott anyag ki lesz emelve a következő képen látható módon.

* Kattintson az **anyag kiosztása a kijelöléshez** gombra az ábrán látható módon. A kiválasztott objektum már hozzá van rendelve a kijelölt objektumhoz.
![hozzárendelési anyag](media/3dsmax/assign-material.jpg)

Az anyag-szerkesztőben a használati esettől függően kiválaszthatja az anyagok típusát széles választékból. Az anyag típusa általában alapértelmezés szerint a **standard** értékre van beállítva. Ez az anyag olyan alapszintű anyag, amely nem alkalmas a PBR beállítására, ezért módosítani kell az anyag típusát egy PBR-anyagra. Az Azure távoli renderelési projektek előnyben részesített **3DSMax** -anyagai a **fizikai anyagok**.

* Az anyag-szerkesztőben kattintson a **standard** fülre, és a megnyíló anyag/Térkép böngészőben válassza a **fizikai anyag**elemet. Ez a művelet a hozzárendelt **szabványos** anyagokat egy pbr **fizikai anyagba**konvertálja.
![fizikai anyag](media/3dsmax/physical-material.jpg)

* Az anyag-szerkesztőben láthatja a fizikai anyagok tulajdonságait (lásd alább), és megkezdheti a textúrák hozzárendelését az eszközhöz.
![textúrák – lista](media/3dsmax/textures-list.jpg)

Ahogy az a fenti képen látható, a térképek és textúrák széles körét lehet felvenni az anyagba. A felhasználási célokra azonban csak öt textúra-tárolóhelyet fogunk használni az anyagban.

>[!TIP]
>A fenti képen látható módon érdemes a megfelelő módon megnevezni az anyagokat.

Most kezdjük megfontolni a textúrák hozzárendelését az anyaghoz. A textúrák előállítása a preferencia szerint változhat, vagy akár a használattól függően is. Előfordulhat például, hogy olyan csempe-textúrákat szeretne használni, amelyek bármely eszközre alkalmazhatók, vagy ha egy projekt/eszköz meghatározott részeit szeretné a saját textúrákkal elválasztani. Előfordulhat, hogy az általános csempe-textúrákat online állapotba kívánja használni, vagy olyan alkalmazásokban szeretné létrehozni, mint például a **Photoshop**, a **Quixel Suite**, az **anyag Suite** stb. 

Mielőtt elkezdenénk a textúrák hozzárendelését, azonban figyelembe kell venniük az eszközök textúrájának koordinátáit (UVW). Habár az ajánlott eljárás, ha bármilyen textúrát alkalmaz a modellre annak érdekében, hogy a modell kicsomagolása megtörténjen (a textúrák nem jelennek meg megfelelően a megfelelő UV-kicsomagolás nélkül), ezért fontos, hogy a modellünk **környezeti elzáródási** leképezést alkalmazzon. A **Maya**-beli **rája-shadertől** eltérően a **3DSMax** található **fizikai anyag** nem rendelkezik dedikált **környezeti elzáródási** felülettel. Ezért alkalmazzuk az AO-térképet egy másik tárolóhelyre, és lehetővé tesszük, hogy azt a többi textúráktól (például a csempe textúrája) elkülönítve lehessen használni, a saját UVW is hozzá kell rendelni. 

Kezdjük azzal, hogy kibontott **UVW-módosítót** rendelünk hozzá a modellhez, ahogy az az alábbi ábrán is látható:

* A kijelölt objektumok tulajdonságok szerkesztőjében kattintson a módosító listára, és a legördülő menüben görgessen lefelé, és válassza a UVW kicsomagolása lehetőséget. Ez a művelet egy kicsomagolatlan UVW-módosítót alkalmaz az eszközre.
![Kicsomagolás – módosító](media/3dsmax/unwrap-modifier.jpg)

* A Térkép csatorna egy értékre van állítva. A térképi csatornán az egyik, hogy a fő kicsomagolása általában megtörténik. Ebben az esetben az objektum ki lett csomagolva, és nem fedi át a textúra koordinátáit (UV).
![nem burkolt – uvw](media/3dsmax/unwrapped-uvw.jpg)

A következő lépés egy második UV-Térkép csatorna létrehozása.

* Ha meg van nyitva, az UV-szerkesztő bezárásához és az UV-menü **szerkesztési** szakaszának csatorna szakaszában módosítsa a csatorna számát két értékre. A Térkép Channel 2 a környezeti Elzáródási térképek várt csatornája. 

* A megnyíló **csatorna változás figyelmeztetési** párbeszédablakban lehetősége lesz **áthelyezni** a meglévő UV-t az 1. csatornán az új 2. csatornába, **vagy fel** kell venni a meglévő UV-t, amely automatikusan létrehoz egy új **UV-kicsomagolást** . Csak akkor válassza a **lemondás** lehetőséget, ha egy új **UV-kicsomagolást** szeretne létrehozni a környezeti elzáródási térképhez, amely eltér az 1. csatornán található UV-fájltól (például ha a Channel 1 csatornán szeretné használni a csempe-textúrákat). A célunk az, hogy az UV-csatornát az egyik csatornáról a 2-es csatornára **helyezzük át** , mivel nem kell szerkeszteni az új UV-csatornát.

>[!NOTE]
>Még ha **áthelyezte** a másolást is – az UV kibontása a Térkép Channel 1 csatornáról a 2. csatornára. az új csatorna UV-re való kicsomagolása az eredeti Térkép csatornájának módosítása nélkül végezhető el.

![csatorna – változás](media/3dsmax/channel-change.jpg)

Az új Térkép-csatorna létrehozásával visszatérhetünk az anyag-szerkesztőben található fizikai anyagokhoz, és megkezdjük a textúrák hozzáadását. Először a környezeti elzáródás (**Ao**) leképezését vesszük fel, mivel van egy további lépés, amely lehetővé teszi, hogy megfelelően működjön. Ha az AO-Térkép csatlakoztatva van az anyaghoz, meg kell utasítania, hogy használja a Map Channel 2 használatát.

* Ahogy azt korábban említettük, nem létezik dedikált tárolóhely az AO Maps számára a **3DSMax fizikai anyagban**. Ehelyett az AO-térképet fogjuk alkalmazni a **diffúz durva** tárolóhelyre.

* A fizikai anyag **általános térképek** listájában kattintson a **diffúz érdesség** **nincs Térkép** tárolóhelyre, és töltse be az Ao-térképet.

* Az AO textúrák tulajdonságainál alapértelmezés szerint a Térkép csatorna beállítása **1** értékre lesz állítva. Módosítsa ezt az értéket **2**értékre. Ez a művelet befejezi a környezeti elzáródási Térkép hozzáadásához szükséges lépéseket.

>[!IMPORTANT]
>Ez egy fontos lépés, különösen akkor, ha a 2. csatornán lévő UV-k eltérnek az 1. csatornán lévők, mivel az Ao nem fog megfelelően kijelölni a nem megfelelő csatornát.

![hozzárendelés – Ao-Map](media/3dsmax/assign-ao-map.jpg)

Most megbirkózunk a normál Térkép hozzárendelésével a PBR-anyagokkal. Ez a művelet némileg eltér a **Maya** -től, hogy a normál Térkép nem alkalmazható közvetlenül a bump Map-tárolóhelyre (az **3DSMax fizikai anyagban** nem található normál térképes tárolóhely), de a normál leképezés-módosítóhoz van rendelve, amely maga a **szokásos** tárolóhelyre van csatlakoztatva.

* A fizikai anyagok tulajdonságai (az anyag-szerkesztőben) a **speciális térképek** szakaszban kattintson a **bump Map** **nincs Térkép** tárolóhelyére. 

* Az anyag/Térkép böngészőben keresse meg és kattintson a **normál bump**elemre. Ez a művelet egy **normál bump** -módosítót ad hozzá az anyaghoz.

* A normál **bump** -módosítóban kattintson a **normál** **térképre, és keresse** meg, majd töltse be a normál térképet.

* Ellenőrizze, hogy a metódus **tangens** értékre van-e állítva (alapértelmezés szerint), és ha szükséges, kapcsolja be a **zöld (Y)** beállítást.

![normál – bump ](media/3dsmax/normal-bump.jpg)
 ![ Load – normál – Térkép](media/3dsmax/load-normal-map.jpg)

A normál Térkép helyes hozzárendelése esetén továbbra is kioszthatja a fennmaradó textúrákat a fizikai anyagok telepítésének befejezéséhez. Ez a folyamat egy egyszerű folyamat, amely nem veszi figyelembe a speciális beállításokat. Az alábbi képen az anyaghoz rendelt textúrák teljes készletét láthatja: ![ all-textúrák](media/3dsmax/all-textures.jpg)

Ha létrehozta és beállította a PBR-anyagokat, érdemes meggondolni a egypéldányos objektumokat a jelenetben. A egypéldányos hasonló objektumok, például a NUTS, a csavarok, a csavaros alátétek – lényegében minden olyan objektum, amely azonos, jelentős megtakarítást eredményezhet a fájlméret szempontjából. A főobjektum példányai rendelkezhetnek a saját méretével, elforgatásával és átalakításával, amelyeket szükség szerint helyezhetnek el a jelenetben. A **3D Studio Max**-ban a **egypéldányos** folyamata egyszerű.

* A fő nézetablakban jelölje ki az exportálni kívánt objektumot/objektumokat.

* Tartsa lenyomva a **SHIFT billentyűt** , és húzza felfelé az eszközöket az átalakító (áthelyezés) eszköz használatával. 

* A megnyíló **klónozási beállítások** párbeszédpanelen állítsa be az **objektumot** a **példányra** , majd kattintson **az OK**gombra. 
![példány – objektum](media/3dsmax/instance-object.jpg)

Ez a művelet létrehozza az objektum egy példányát, amely a szülőtől és a szülő más példányaitól függetlenül elforgatható vagy méretezhető.

>[!IMPORTANT]
>Azonban – az egyes példányokon végrehajtott módosításokat az objektum összes példánya számára továbbítjuk, így ha egy példányos objektum-(csúcspont-) és sokszög-felülettel dolgozik, akkor ügyeljen arra, hogy az összes ilyen példányra hatással legyen. Ne feledje, hogy minden példányban lévő objektum bármikor elvégezhető egy egyedi objektumba. 

>[!TIP]
>A jelenet egypéldányos kapcsolatos ajánlott eljárás az, hogy a későbbiekben is létrehozhatja őket, mivel a **példányok példányainak későbbi lecserélése rendkívül** nehéz. 

Az exportálási folyamatba való áttérés előtt az egyik utolsó dolog az, hogy hogyan érdemes a jelenetét/eszközét megosztani a megosztáshoz. Ideális esetben, ha az adategységet egy ügyfélhez vagy csapattaghoz továbbítja, akkor szeretné, hogy az eszköz megnyissa és megtekintse az adategységet, ahogy azt a lehető legkevesebb feldolgozónak kell tekinteni. Ezért fontos, hogy az adategységek textúrájának elérési útjai a jelenet fájlhoz viszonyítva maradjanak. Ha az eszköz textúrájának elérési útjai egy helyi meghajtóra vagy abszolút elérési útra vagy helyre mutatnak, akkor nem töltődnek be a jelenetbe, ha egy másik számítógépen van megnyitva, még akkor is, ha a **. max** fájl ugyanabban a mappában található, mint a textúrák. A textúra elérési útjaihoz viszonyítva a 3D Studio Max a probléma megoldásához és viszonylag egyszerű.

* A fő eszköztáron válassza a **fájl**  >  **hivatkozási**  >  **eszköz követése váltógomb**lehetőséget. 

* A megnyíló Asset Tracking böngészőben megtekintheti a PBR-anyagokhoz a **Maps/Shaders** oszlopban felsorolt összes textúrákat.

* A **teljes elérési utat** tartalmazó oszlopban megjelenik a textúrák helyének elérési útja, amely valószínűleg a helyi gépen található.

* Végül egy **állapot**nevű oszlopot fog látni. Ebben az oszlopban látható, hogy egy adott textúra található-e, és alkalmazva lett-e a jelenetre, és a textúrát a következő kifejezések egyikével látja el: **OK**, **talált**vagy **hiányzó fájl**. Az első két érték azt jelzi, hogy a fájl megtalálható és be van töltve, míg az utolsó, természetesen azt jelenti, hogy a követő nem talált fájlt.
![textúra – elérési utak](media/3dsmax/texture-paths.jpg)

Észreveheti, hogy az összes textúrája nem jelenik meg az eszköz első megnyitásakor. Nem kell aggódnia, mert a Path-Finding folyamat egyszer vagy kétszer általában megkeresi a jelenet összes textúráját. A Path-Finding folyamat a következő: 

* Az eszköz nyomon követése ablakban a **Shift** + **Maps/Shaders** listában**kattintson** a felső textúrára, és folytassa a SHIFT billentyűt a lista utolsó textúrájának megjelenítéséhez. Ezzel a művelettel kiválaszthatja a listában szereplő összes textúrát. A kiválasztott textúrák a kék színnel lesznek kiemelve (lásd a fenti képen).

* Kattintson a jobb gombbal a kiválasztásra, majd a megnyíló előugró menüben válassza az **elérési út beállítása**lehetőséget.

* Az megnyíló **eszköz elérési útjának megadása** mezőben válassza ki a textúrák helyi elérési útját, és cserélje le a következőre, `.\` majd kattintson az **OK**gombra. 

* Egy adott idő elteltével (amely attól függően változik, hogy hány textúrák van a jelenetben, és milyen nagy a jelenet). az Asset Tracker a következőképpen oldja fel magát (lásd a képet).
![megoldás – textúrák](media/3dsmax/resolve-textures.jpg)

Figyelje meg, hogy a **teljes elérési út** oszlop most üres. Ez azt jelenti, hogy a jelenet már nem keresi meg a megfelelő textúrákat egy adott (abszolút) helyen, de mindig megkeresi őket, amíg a maximális fájl vagy a kapcsolódó FBX-fájl ugyanabban a mappában található, mint a textúrák. 

>[!NOTE]
>Időnként előfordulhat, hogy az összes textúrák és elérési út megkereséséhez és feloldásához többször is meg kell ismételnie ezt a folyamatot. Nem kell aggódnia, csak ismételje meg a műveletet, amíg az összes érintett eszközt el nem veszi. Előfordulhat, hogy néhány fájl már nem található. Ebben az esetben egyszerűen jelölje ki a listában az összes objektumot, majd kattintson a **Hiányzó elérési utak eltávolítása** elemre (lásd a fenti képen).

## <a name="fbx-export"></a>FBX-exportálás

Az eszköz nyomon követésének befejezése után most már átléphet a FBX exportálási szolgáltatásba. A folyamat is egyszerű, és többféleképpen is elvégezhető. 

>[!TIP]
>Ajánlott, hogy ha a teljes jelenetet exportálni szeretné, csak a szükséges eszközök exportálását választja. Különösen az erőforrás-igényes jeleneteknél az Exportálás hosszú időt is igénybe vehet, így érdemes csak a szükséges mennyiségű exportálást exportálni
>
>Javasoljuk, hogy ha olyan módosítókat használ, mint például a **Turbosmooth** vagy az **Open SubDiv** , amelyeket az exportálás előtt össze kell csuknia, mert problémákat okozhatnak az exportálás során. A jelenet előtt mindig mentse a jelenetet! 

* A jelenetben jelölje ki az exportálni kívánt eszközöket, és a fő eszköztáron válassza a fájl exportálás **File**  >  **Exportálás**  >  **kiválasztva** lehetőséget.

* Az **exportálandó fájl kiválasztása** párbeszédpanelen írja be vagy válassza ki a kimeneti fájl neve lehetőséget, majd a **mentési típus** beállításainál válassza az **Autodesk (*. FBX)** elemet. Ez a művelet megnyitja a FBX exportálás menüt. 

* Ne feledje, hogy ha a jelenetben olyan példányokat hozott létre, amelyek fontosak, hogy a **példányok megőrzése** a FBX exportálási beállításainál legyen bekapcsolva. 
![FBX – exportálás](media/3dsmax/fbx-export.jpg)

Ne feledje, hogy korábban már említettük, hogy a fájl exportálásának néhány módja volt. Ha az Exportálás célja, hogy a FBX egy mappában vagy könyvtárban található textúrákkal együtt kell megosztani, akkor az alábbi képen látható beállításokat kell alkalmaznia, és jól kell működnie. Miután kiválasztotta a beállításokat, kattintson **az OK**gombra.
![FBX – beállítások](media/3dsmax/fbx-settings.jpg)

Ha azonban inkább nem szeretné megosztani a nagy méretű mappákat/könyvtárakat a textúrákhoz és a FBX, akkor választhatja a textúrák **beágyazását** a FBX. Ez azt jelenti, hogy a teljes adategység – textúrákat tartalmaz – egyetlen FBX lesz hozzáadva. Ügyeljen arra, hogy bár az exportálást egyetlen objektumba kombinálja, hogy a FBX-fájl jelentősen nagyobb lesz.

>[!IMPORTANT]
>Ha az eredményül kapott FBX-fájl 2.4 GB-nál nagyobb, akkor a FBX-exportálási beállítások minimális verziója (lásd fent) 2016 vagy újabb lehet. Mivel az újabb verziókban 64 bites támogatás van, így a nagyobb fájlok is támogatottak.

* A FBX-exportálási beállítások között kapcsolja be a * * adathordozó beágyazása elemet, majd kattintson **az OK** gombra a tartalmazott textúrákkal való exportáláshoz. 

Ha fizikai anyagot használ a FBX való exportálás során, a rendszer valószínűleg a következő figyelmeztetést fogja látni az exportálási párbeszédben az OK gombra kattintás után: ![ Exportálás – figyelmeztetések](media/3dsmax/export-warnings.jpg)

Ez a figyelmeztetés egyszerűen tájékoztatja a felhasználót arról, hogy az exportált anyagok nem kompatibilisek más szoftveres csomagokkal. Mivel a fizikai anyag kompatibilis az Azure távoli renderelésével, nem kell aggódnia. A folyamat befejezéséhez és az ablak bezárásához egyszerűen kattintson **az OK** gombra.

## <a name="conclusion"></a>Összegzés

Általánosságban elmondható, hogy az ilyen típusú anyagok reálisak, mert a fény valós fizikáján alapulnak. Ez egy további alámerülés hatást eredményez, amely úgy tűnik, hogy a jelenet valóban létezik a valós világban.

## <a name="next-steps"></a>További lépések

Most már tudja, hogyan állíthat be speciális világítású anyagokat a jelenetekben található objektumokhoz. Arról is tájékozódhat, hogyan exportálhatja az objektumokat az Azure távoli renderelés által támogatott FBX formátumba. A következő lépés a FBX fájl átalakítása és megjelenítése az Azure-beli távoli renderelésben.

>[!div class="nextstepaction"]
>[Gyors útmutató: modell átalakítása renderelésre](../../quickstarts\convert-model.md)