---
title: Fizikai alapú renderelési anyagok beállítása a Maya-ban
description: Elmagyarázza, hogyan állíthat be fizikailag alapú renderelési anyagokat a Maya-ben, és hogyan exportálhatja őket FBX formátumba.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 72742ff4f6aa19fda092b44d8d2237e7d49dd816
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85373241"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Oktatóanyag: fizikailag alapú renderelési anyagok beállítása a Maya-ban

## <a name="overview"></a>Áttekintés
Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Az anyagokat speciális megvilágítás használatával rendelheti hozzá a jelenetben lévő objektumokhoz.
> * Objektumok és anyagok egypéldányos kezelése.
> * FBX-formátumba exportálhatja a jeleneteket, és kiválaszthatja a fontos beállítások lehetőséget.

A [fizikai alapú renderelési (pbr) anyagok](../../overview/features/pbr-materials.md) létrehozása a Maya-ben viszonylag egyszerű feladat. Számos módon hasonló a PBR-telepítőhöz más tartalom-létrehozási alkalmazásokban, például a 3DS max-ban. Ez az oktatóanyag az Azure-beli távoli renderelési projektek alapszintű PBR shader-telepítésének és FBX-exportálásának útmutatója. 

Az oktatóanyagban szereplő minta jelenet számos sokszög típusú objektumot tartalmaz. Különböző anyagokat rendelnek hozzájuk, például fa, fém, festett fém, műanyag és gumi. Általánosságban elmondható, hogy minden anyag a következő textúrákat tartalmazza:

* **Albedó**, amely az anyagok színleképezése, és más néven **diffúz** vagy **BaseColor**.
* Fémesség, amely meghatározza, hogy egy anyag fémes- **e, és**hogy mely részek fémesek. 
* A **érdesség**, amely meghatározza, hogy a felületek milyen durva vagy egyenletesek legyenek, és hatással vannak a reflexiók és a kiemelések élességére vagy homályos a felületen.
* **Normál**, amely részletes adatokat ad hozzá egy felülethez anélkül, hogy további sokszögeket kellene hozzáadnia. A részletek közé tartozhatnak például a fém felületen vagy a gabona faanyagban való kihúzása.
* **Környezeti elzáródás**, amely lágy árnyékolás hozzáadására és az árnyékok a modellhez való kapcsolódására szolgál. Ez egy szürkeárnyalatos Térkép, amely azt jelzi, hogy a modell mely területein kap teljes világítást (fehér) vagy teljes árnyalatot (fekete). 

## <a name="prerequisites"></a>Előfeltételek
* Autodesk Maya 2017 vagy újabb

## <a name="set-up-materials-in-the-scene"></a>Anyagok beállítása a jelenetben
A következő módon állíthatja be a PBR-anyagokat a Maya alkalmazásban.

Ahogy a minta jelenetben is látható, létrehoztunk több Box objektumot is. Minden objektum más típusú anyagot képvisel. Ahogy az ábrán is látható, ezek az objektumok a megfelelő nevet kaptak.

Az Azure-alapú távoli renderelés mérőműszereket használ a méréshez, a felfelé irány pedig az Y tengely. Mielőtt megkezdené az eszközök létrehozását, azt javasoljuk, hogy a jelenet egységeit a Maya-ben állítsa be. Az exportáláshoz állítsa a FBX exportálási beállítások mértékegységek mérőszámait.

> [!TIP]
> Adja meg a modellnek megfelelő neveket a megfelelő rész vagy anyag típusa alapján. Az értelmes nevek megkönnyítik az objektum – nehéz jelenetek átjárását.

![Objektumok nevei](media/object-names.jpg)

Miután létrehozta vagy beszerzett néhány textúrát, létrehozhat egyedi textúrákat is. Olyan anyagmintás alkalmazásokat is használhat, mint például a Quixel Suite, a PhotoShop vagy az anyag Suite, vagy az általános csempe-textúrákat más forrásokból is lekérheti.

Textúrák alkalmazása a modellre:

1. A jelenet nézőpontjában válassza ki a modellt vagy a geometriát, majd kattintson rá a jobb gombbal. A megjelenő menüben válassza az **új anyag kiosztása**elemet.
1. Az **új anyag kiosztása** párbeszédpanelen nyissa meg a **Maya**  >  **rája PBS**-t. Ez a művelet egy PBR-anyagot rendel a modellhez. 

A Maya 2020-ben számos különböző PBR-árnyékolás érhető el. Ide tartoznak a **Maya standard Surface**, az **Arnold standard Surface**és a **rája pbr**. A **Maya standard Surface shader** még nem exportálható a FBX 2020 beépülő modulon keresztül. Az **Arnold standard Surface SHADER** FBX-fájlokkal is exportálható. A legtöbb tekintetben megegyezik a **Maya standard Surface shader**szolgáltatással. Ez hasonló a **fizikai anyagokhoz** a 3D Studio Max-ban.

A **rája pbr shader** számos más alkalmazással kompatibilis, és a legszorosabban megfelel az Azure Remote rendering követelményeinek. A Maya 2017 óta támogatott. Ha az ilyen típusú anyagok vizualizációja a nézetablakban történik, az az Azure-beli távoli renderelés során később láthatóvá válik.

![Rája anyagok](media/stingray-material.jpg)

Az objektumhoz hozzárendelt, és a megfelelő névvel ellátott anyagokkal már hozzárendelheti a különböző textúrákat is. Az alábbi képek azt mutatják be, hogy az egyes textúra-típusok hol vannak a PBR-anyagokba. A rája PBR-anyagok segítségével kiválaszthatja, hogy mely attribútumok közül lehet aktiválni. A textúratérkép csatlakoztatása előtt aktiválnia kell a megfelelő attribútumokat.

![Anyag beállítása](media/material-setup.jpg)

Megfelelő módon nevezze el az anyagokat a használat vagy a típus figyelembevételével. Az egyedi részekben használt anyagok neve az adott rész számára is megadható. A területeken szélesebb körben használt anyagok neve megadható a tulajdonságának vagy típusának.

Rendelje hozzá a textúrákat a képen látható módon.

![Textúra beállítása](media/texture-setup.jpg)

Ha létrehozta és beállította a PBR-anyagokat, érdemes lehet [egypéldányos objektumokat](../../how-tos/conversion/configure-model-conversion.md#instancing) létrehozni a jelenetben. A egypéldányos hasonló objektumok, például a NUTS, a csavarok, a csavarok és a mosók jelentős megtakarításokat eredményeznek a fájlméretben. A főobjektum példányai rendelkezhetnek a saját méretezésével, elforgatásával és átalakításával, így szükség szerint helyezhetők el a jelenetben. 

A Maya-ben a egypéldányos folyamata egyszerű.

1. A **Szerkesztés** menüben nyissa meg a **speciális** lehetőséget a megnyitáshoz.
1. A **Speciális beállítások duplikálása** párbeszédpanelen, a **geometria típusa** mezőben válassza a **példány** lehetőséget. 
1. Válassza az **ismétlődő speciális**lehetőséget.

   ![Egypéldányos](media/instancing.jpg)

Ez a művelet létrehozza az objektum egy példányát. A szülőtől és a szülő más példányaitól függetlenül áthelyezheti, elforgathatja vagy méretezheti is. 

Az egyes példányokon végrehajtott módosítások az objektum összes példányára továbbítódnak. Használhat például egy példányban lévő objektum összetevőit, például a csúcspontokat és a sokszögeket. Győződjön meg arról, hogy az összes ilyen példányra hatással van. 

A minta jelenetben minden egyes Box-objektum példánya meg lett szakítva. Ez a művelet a jelenet FBX formátumba való exportálásakor lesz releváns.

![A jelenet áttekintése](media/scene-overview.jpg)

> [!TIP]
> Hozzon létre példányokat a jelenetben. A példányokat tartalmazó példányok későbbi cseréje rendkívül nehéz. 

## <a name="fbx-export-process"></a>FBX-exportálási folyamat

Térjünk át a jelenet vagy a jelenet FBX exportálásához. Az eszközök exportálásakor érdemes csak azokat az objektumokat vagy eszközöket kijelölni, amelyek az exportálni kívánt jelenetből származnak. Előfordulhat például, hogy egy jelenetben 100 objektum található. Ha csak 30 közülük kívánja használni, nincs értelme a teljes jelenet exportálásának. 

Válassza ki a következőt:

1. **File**  >  Nyissa meg a fájl**exportálása lehetőséget** a kijelölés **exportálása** párbeszédpanel megnyitásához.
1. A **fájltípusok** mezőben válassza a **FBX EXPORTÁLÁS** lehetőséget a FBX exportálási beállításainak megjelenítéséhez. A FBX-exportálás elsődleges beállításai piros színnel jelennek meg a képen.

   ![FBX exportálása](media/FBX-exporting.jpg)

A követelményektől függően előfordulhat például, hogy egy adategységet szeretne küldeni egy ügyfélnek. Lehet, hogy nem szeretne nagy mennyiségű textúra-fájlt küldeni az objektummal. Kiválaszthatja, hogy beágyazza a textúrákat az exportált FBX-fájlba. Ez a beállítás azt jelenti, hogy csak egy fájlt kell becsomagolni, de a FBX-eszköz mérete jelentősen megnő. Engedélyezheti a textúrák beágyazásának lehetőségét a **média beágyazása** lehetőség kiválasztásával az ábrán látható módon.

> [!TIP]
> Ebben a példában a fájl neve, hogy tükrözze ezt a feltételt. Ez a névadási stílus jó módszer az eszközök nyomon követésére. 

Miután beállította az exportálási konfigurációt, a jobb alsó sarokban válassza a **kijelölés exportálása** lehetőséget.

![Adathordozók beágyazása](media/embedding-media.jpg)

## <a name="conclusion"></a>Összegzés

Általánosságban elmondható, hogy az ilyen típusú anyagok reálisak, mert a fény valós fizikáján alapulnak. Ez egy további alámerülés hatást eredményez, amely úgy tűnik, hogy a jelenet valóban létezik a valós világban.

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan állíthat be speciális világítású anyagokat a jelenetekben található objektumokhoz. Arról is tájékozódhat, hogyan exportálhatja az objektumokat az Azure távoli renderelés által támogatott FBX formátumba. A következő lépés a FBX fájl átalakítása és megjelenítése az Azure-beli távoli renderelésben.

> [!div class="nextstepaction"]
> [Gyors útmutató: modell átalakítása renderelésre](../../quickstarts\convert-model.md)