---
title: PBR anyagok
description: A PBR anyagtípusát írja le.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680257"
---
# <a name="pbr-materials"></a>PBR anyagok

*A PBR-anyagok* az Azure távoli renderelése egyik támogatott [anyagtípusa.](../../concepts/materials.md) Olyan olyan [tűnések esetén használatosak,](../../concepts/meshes.md) amelyeknek reális megvilágítást kell kapniuk.

A PBR a **P**hisztérikus **B**ased **R**endering rövidítése, és azt jelenti, hogy az anyag fizikailag kézenfekvő módon írja le a felület vizuális tulajdonságait, így minden megvilágítási körülmény között reális eredmények lehetségesek. A legtöbb modern játékmotor és tartalomkészítő eszköz támogatja a PBR-anyagokat, mivel a valós idejű rendereléshez a valós világ forgatókönyveinek legjobb közelítése.

![Sisak glTF minta modell által renderelt ARR](media/helmet.png)

A PBR anyagok azonban nem univerzális megoldások. Vannak olyan anyagok, amelyek a betekintési szögtől függően eltérő színeket tükröznek. Például, néhány szövet vagy autó festékek. Az ilyen típusú anyagokat nem kezeli a szabványos PBR-modell, és jelenleg nem támogatja az Azure Remote Rendering. Ez magában foglalja a PBR kiterjesztéseket, mint például *a vékonyfóliát* (többrétegű felületek) és a *Clear-Coat -t* (autófestékek hez).

## <a name="common-material-properties"></a>Közös anyagtulajdonságok

Ezek a tulajdonságok minden anyagra közösek:

* **albedoColor:** Ezt a színt megszorozzuk más színekkel, például az *albedoMap* vagy *a csúcspont színekkel.* Ha egy anyagon engedélyezve van az *átlátszóság,* az alfa-csatorna az opacitás beállítására szolgál, teljesen átlátszatlan és `1` `0` teljesen átlátszó jelentéssel. Az alapértelmezett érték a fehér.

  > [!NOTE]
  > Ha a PBR anyag teljesen átlátszó, mint egy tökéletesen tiszta üvegdarab, még mindig tükrözi a környezetet. Fényes foltok, mint a nap még mindig látható a reflexió. Ez más a [színes anyagok](color-materials.md).

* **albedoMap:** [2D textúra](../../concepts/textures.md) a képpontonkénti albedo értékekhez.

* **alphaClipEnabled** és **alphaClipThreshold:** Ha *alphaClipEnabled* igaz, minden pixel, ahol az albedo alfa értéke alacsonyabb, mint *alphaClipThreshold* nem lesz megrajzolva. Alfa nyírás lehet használni anélkül, hogy az átláthatóság és sokkal gyorsabb teszi. Alpha nyírt anyagok még mindig lassabb, hogy tegye, mint a teljesen átlátszatlan anyagok, mégis. Alapértelmezés szerint az alfa-vágás le van tiltva.

* **textureCoordinateScale** és **textureCoordinateOffset:** A skála megszorozva lesz az UV textúra koordinátáiban, az eltolás hozzáadódik. Lehet használni, hogy nyúlik, és a shift a textúrák. Az alapértelmezett lépték (1, 1) és az eltolás (0, 0).

* **useVertexColor:** Ha a háló csúcspontszíneket tartalmaz, és ez a beállítás engedélyezve van, a hálók csúcspontjainak csúcspontjai megszorozzák az *albedoColor* és *albedoMap színeket.* Alapértelmezés szerint a csúcspont színei le vannak tiltva.

* **isDoubleSided:** Ha a kétoldalas ság igaz értékre van állítva, az ezzel az anyaggal rendelkező háromszögek akkor is megjelennek, ha a kamera a hátukon van. A PBR anyagok világítás is megfelelően kiszámítani a hátsó lapok. Alapértelmezés szerint ez a beállítás le van tiltva. Lásd még: [Egyoldalas renderelés](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>PBR anyag tulajdonságai

A fizikai alapú renderelés alapgondolata a *BaseColor,* *Metalness*és *Roughness* tulajdonságok használata a valós anyagok széles körének emulálásához. A PBR részletes leírása túlmutat e cikk hatályán. A PBR-ről további információt [további forrásokban](http://www.pbr-book.org)talál. A következő tulajdonságok a PBR-anyagokra vonatkoznak:

* **baseColor:** A PBR-anyagokban az *albedo színt* *alapszínnek*nevezik. Az Azure Remote Renderelés az *albedo szín* tulajdonság már jelen van a közös anyag tulajdonságait, így nincs további alapszín tulajdonság.

* **érdesség** és **érdességTérkép: Az** érdesség határozza meg, hogy a felület milyen durva vagy sima. A durva felületek több irányba szórják a fényt, mint a sima felületek, amelyek a tükröződéseket inkább homályossá teszik, mint élessé. Az értéktartomány `0.0` a-hoz `1.0`terjed. Ha `roughness` `0.0`egyenlő, a tükröződések élesek lesznek. Ha `roughness` `0.5`egyenlő, a tükröződések elmosódnak.

  Ha mind érdességi értéket, mind érdességi térképet ad meg, a végső érték a kettő terméke lesz.

* **metalness** és **metalnessMap:** A fizika, ez a tulajdonság megfelel, hogy a felület vezető vagy dielektromos. Vezetőképes anyagok különböző fényvisszaverő tulajdonságokkal, és általában fényvisszaverő nélkül albedo szín. A PBR-anyagokban ez a tulajdonság befolyásolja, hogy egy felület mennyire tükrözi a környező környezetet. Az értékek `0.0` `1.0`a és a között mozognak. Amikor a `0.0`fémesség , az albedo színe teljesen látható, és az anyag úgy néz ki, mint a műanyag vagy kerámia. Amikor a `0.5`fémesség, úgy néz ki, mint a festett fém. Amikor a `1.0`fémesség , a felület szinte teljesen elveszti albedo színét, és csak tükrözi a környezetet. Például, `metalness` ha `1.0` `roughness` van, és akkor `0.0` egy felület úgy néz ki, mint a valós tükör.

  Ha mind a fémességi értéket, mind a fémességi térképet szállítjuk, a végső érték a kettő terméke lesz.

  ![fémesség és érdesség](./media/metalness-roughness.png)

  A fenti képen a jobb alsó sarokban lévő gömb úgy néz ki, mint egy igazi fémanyag, a bal alsó úgy néz ki, mint a kerámia vagy műanyag. Az albedo színe is változik a fizikai tulajdonságok nak megfelelően. A növekvő érdesség, az anyag elveszti reflexió élességét.

* **normalMap:** A finom szemcsés részletek szimulálása érdekében [normál térkép](https://en.wikipedia.org/wiki/Normal_mapping) et lehet biztosítani.

* **okclusionMap** és **aoScale:** [A környezeti elzáródás](https://en.wikipedia.org/wiki/Ambient_occlusion) valósághűvé teszi a hasadékokkal rendelkező objektumokat azáltal, hogy árnyékokat ad az elzárt területekhez. Az elzáródási `1.0`érték `0.0` a -tól a `1.0` -ig `0.0` terjed, ahol sötétséget jelent (elzárva), és azt jelenti, hogy nincs elzáródás. Ha egy 2D textúra elzáródási térképként van megadva, a hatás engedélyezve van, és az *aoScale* szorzóként működik.

  ![Okklúziós térkép](./media/boom-box-ao2.gif)

* **átlátható:** PBR-anyagok esetében csak egy áttetszőségi beállítás van: engedélyezve van-e vagy sem. Az opacitást az albedo szín alfa-csatornája határozza meg. Ha engedélyezve van, egy összetettebb renderelési folyamat ot hív meg a rendszer a félig átlátszó felületek rajzolására. Az Azure Remote Rendering valós [sorrendfüggetlen átláthatóságot](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT) valósít meg.

  Az átlátszó geometria renderelése költséges. Ha csak lyukakra van szüksége egy felületen, például egy fa leveleinél, akkor jobb, ha inkább alfa vágást használ.

  ![Átláthatósági](./media/transparency.png) közlemény a fenti képen, hogy a jobb szélső gömb teljesen átlátszó, de a reflexió még mindig látható.

  > [!IMPORTANT]
  > Ha bármely anyagot átlátszatlanról átlátszóra kell váltani futásidőben, a renderelőnek a *TileBasedComposition* [renderelési módot](../../concepts/rendering-modes.md)kell használnia. Ez a korlátozás nem vonatkozik azokra az anyagokra, amelyeket átlátszó anyagként alakítanak át.

## <a name="technical-details"></a>Technikai részletek

Az Azure Remote Rendering a Cook-Torrance mikrodimenziós BRDF-et használja a GGX NDF, Schlick Fresnel és egy GGX Smith kifejezéssel, amely lamberti szórt kifejezéssel korrelál. Ez a modell jelenleg a de facto ipari szabvány. További részletes részleteket ebben a cikkben talál: [Fizikai alapú renderelés - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Az Azure távoli renderelésben használt *Metalness-Roughness* PBR modell alternatívája a *Specular-Glossiness* PBR modell. Ez a modell az anyagok szélesebb körét képviselheti. Azonban ez drágább, és általában nem működik jól a valós idejű esetekben.
Nem mindig lehet a *Specular-Glossiness-ről* *metalness-érdességgé* konvertálni, mivel vannak *olyan (diffúz, specular) értékpárok,* amelyeket nem lehet *átváltani (BaseColor, Metalness)* értékpárrá. A másik irányban történő átalakítás egyszerűbb és pontosabb, mivel az összes *(BaseColor, Metalness)* pár jól meghatározott *(szórványos, specular)* pároknak felel meg.

## <a name="next-steps"></a>További lépések

* [Színes anyagok](color-materials.md)
* [Textúrák](../../concepts/textures.md)
* [Rácsvonalak](../../concepts/meshes.md)
