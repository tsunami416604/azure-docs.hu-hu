---
title: TexConv - Textúra átalakító eszköz
description: Használati utasítás a TexConv parancssori eszközhöz
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680023"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Textúra átalakító eszköz

A TexConv egy parancssori eszköz a textúrák tipikus beviteli formátumokból, például a PNG, a TGA, a JPEG és a DDS optimalizált formátumokba történő feldolgozásához a futásidejű felhasználáshoz.
Míg a leggyakoribb forgatókönyv az, hogy `A.xxx` konvertálni `B.yyy`egy bemeneti fájlt egy optimalizált formátumban, az eszköz számos további lehetőségek speciális használatra.

## <a name="command-line-help"></a>Parancssori súgó

Ha a TexConv.exe-t a `--help` paraméterrel futtatja, az összes rendelkezésre álló lehetőség megjelenik. Ezenkívül a TexConv kinyomtatja a használt beállításokat, amikor végrehajtják, hogy segítsen megérteni, mit csinál. A részletekért tekintse meg ezt a kimenetet.

## <a name="general-usage"></a>Általános használat

A TexConv mindig **pontosan egy kimeneti** fájlt állít elő. Több **bemeneti** fájlt is használhat a kimenet összeállításához. A szerelvény, azt is szüksége van egy **csatorna leképezése**, amely megmondja, hogy melyik csatorna *(Piros, Zöld, Kék* vagy *Alfa*), hogy melyik bemeneti fájlt, és mozgassa azt, amely csatorna a kimeneti kép.

A legtöbb egyenes előre parancssor a következő:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`a kimeneti fájlt és -formátumot adja meg
- `-in0`az első bemeneti kép megadása
- `-rgba`azt mondja, hogy a kimeneti képnek mind a négy csatornát használnia kell, és hogy 1:1 arányban kell venni a bemeneti képből

## <a name="multiple-input-files"></a>Több bemeneti fájl

Ha több bemeneti fájl kimenetét szeretné összeállítani, adja meg az egyes bemeneti fájlokat a `-in` növekvő számú beállítással:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

2D textúrákból történő kockatérkép összeállításakor `-right`használhatja `-top` `-bottom`a `-front` `-back` , `-px` `-left`, `-py` `-ny`, `-pz` `-nz`, vagy , `-nx`, , , , , .

A bemenetek kimeneti fájlhoz való hozzárendeléséhez megfelelő csatornaleképezésre van szükség.

## <a name="channel-mappings"></a>Csatornaleképezések

A csatornaleképezési beállítások határozzák meg, hogy melyik bemenetből töltse ki az adott kimeneti csatornákat. Az egyes csatornák bemenetét az így megfelelően adhatja meg:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Itt a kimenet RGB csatornái az első bemeneti képpel lesznek feltöltve, de a piros és a kék csere lesz. A kimenet alfa-csatornája a második bemeneti kép piros csatornájának értékeivel lesz feltöltve.

Az egyes csatornák leképezésének külön-külön történő megadása a legnagyobb rugalmasságot biztosítja. A kényelem érdekében ugyanez lehet írni a "swizzling" operátorok:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Kimeneti csatornák

A következő csatornaleképezési beállítások érhetők el:

- `-r`, `-g` `-b`, `-a` , : Ezek egycsatornás hozzárendeléseket határoznak meg
- `-rg`: Adja meg a piros és zöld csatorna hozzárendeléseket.
- `-rgb`: Adja meg a piros, zöld és kék csatorna hozzárendeléseket.
- `-rgba`: Mind a négy csatorna-hozzárendelést megadja.

Csak az R, RG vagy RGB csatornát említve utasítja a TexConv-ot, hogy hozzon létre egy kimeneti fájlt, amely csak 1, 2 vagy 3 csatornával rendelkezik.

### <a name="input-swizzling"></a>Bemeneti swizzling

Amikor megadja, hogy melyik bemeneti textúrának kell kitöltenie melyik kimeneti csatornát, a bemenetet fel lehet pisnyelni:

- `-rgba in0`egyenértékű a`-rgba in0.rgba`
- `-rgba in0.bgra`felpezsdül a bemeneti csatornák
- `-rgb in0.rrr`a piros csatornát az összes csatornára

A csatornákat fekete vagy fehér színnel is kitöltheti:

- `-rgb in0 -a white`létrehoz egy 4 csatornás kimeneti textúrát, de az alfa teljesen átlátszatlan
- `-rg black -b white`létrehoz egy teljesen kék textúra

## <a name="common-options"></a>Gyakori beállítások

A legérdekesebb lehetőségek az alábbiakban vannak felsorolva. További lehetőségek vannak `TexConv --help`felsorolva .

### <a name="output-type"></a>Kimenet típusa

- `-type 2D`: A kimenet egy normál 2D-s kép lesz.
- `-type Cubemap`: A kimenet egy kockatérképes kép lesz. Csak DDS kimeneti fájlok esetén támogatott. Ha ez meg van adva, a kockatérképet 6 normál 2D bemeneti lemezképből lehet összeállítani.

### <a name="image-compression"></a>Képtömörítés

- `-compression none`: A kimeneti kép tömörítetlen lesz.
- `-compression medium`: Ha támogatott, a kimeneti kép tömörítést használ a túl sok minőség feláldozása nélkül.
- `-compression high`: Ha támogatott, a kimeneti kép tömörítési és áldozati minőséget használ egy kisebb fájl javára.

### <a name="mipmaps"></a>Miptérképek

Alapértelmezés szerint a TexConv miptérképeket hoz létre, amikor a kimeneti formátum támogatja.

- `-mipmaps none`: A miptérképek nem jönnek létre.
- `-mipmaps Linear`: Ha támogatott, a mipmaps egy dobozszűrő használatával jön létre.

### <a name="usage-srgb--gamma-correction"></a>Használat (sRGB / gamma korrekció)

A `-usage` beállítás meghatározza a kimenet célját, és így megmondja a TexConv-nak, hogy alkalmazza-e a gamma korrekciót a bemeneti és kimeneti fájlokra. A használat csak az RGB csatornákat érinti. Az alfa-csatorna mindig "lineáris" értékeket tartalmaz. Ha a használat nincs megadva, az "automatikus" mód megpróbálja észlelni az első bemeneti lemezkép formátumából és fájlnevéből származó használatot. Például az egy- és kétcsatornás kimeneti formátumok mindig lineárisak. Ellenőrizze a kimenetet, hogy milyen döntést hozott a TexConv.

- `-usage Linear`: A kimeneti kép olyan értékeket tartalmaz, amelyek nem színeket jelölnek. Ez általában a helyzet a fémes és érdes ség textúrák, valamint mindenféle maszkok.

- `-usage Color`: A kimeneti kép színeket jelöl, például diffúz/albedo térképeket. Az sRGB-jelző a kimeneti DDS-fejlécben lesz beállítva.

- `-usage HDR`: A kimeneti fájlnak képpontonként több mint 8 bitet kell használnia a kódoláshoz. Ennek következtében az összes érték lineáris térben van tárolva. HDR textúrák esetén nem számít, hogy az adatok színt vagy más adatot jelölnek.For HDR textúrák it does not matter whether the data represents color or other data.

- `-usage NormalMap`: A kimeneti kép egy érintőtérű normál térképet jelöl. Az értékek normalizálódnak, és a mipmap számítás a kissé lesz optimalizálva.

- `-usage NormalMap_Inverted`: A kimenet egy érintőtérű normál térkép, amelynek Y-ja a bemenettel ellentétes irányba mutat.

### <a name="image-rescaling"></a>Kép átméretezése

- `-minRes 64`: Megadja a kimenet minimális felbontását. Ha a bemeneti kép kisebb, akkor felskálázott lesz.
- `-maxRes 1024`: Megadja a kimenet maximális felbontását. Ha a bemeneti kép nagyobb, akkor leméreteződik.
- `-downscale 1`: Ha ez nagyobb, mint 0, a bemeneti képek a felére csökkennek az N felbontásban. Ezzel általános minőségcsökkentést alkalmazhat.

## <a name="examples"></a>Példák

### <a name="convert-a-color-texture"></a>Színtextúra konvertálása

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Normál térkép konvertálása

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>HDR-kockatérkép létrehozása

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

A HDR kockatérképek nagyszerű forrása [a hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Süssünk több képet egy

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Egyetlen csatorna kinyerése

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
