---
title: TexConv – textúra átalakító eszköz
description: A TexConv parancssori eszköz felhasználói kézikönyve
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80680023"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv – textúra átalakító eszköz

A TexConv egy parancssori eszköz, amely a szokásos bemeneti formátumokból, például a PNG, a TGA, a JPEG és a DDS használatával dolgozza fel a textúrákat a futásidejű felhasználáshoz optimalizált formátumba.
Habár a leggyakoribb forgatókönyv egy bemeneti fájl `A.xxx` optimalizált formátumba konvertálása `B.yyy` , az eszköz számos további lehetőséget kínál a speciális használatra.

## <a name="command-line-help"></a>Parancssori Súgó

TexConv.exe futtatása a (z `--help` ) paraméterrel az összes elérhető beállítást felsorolja. Emellett a TexConv kinyomtatja a használatban lévő beállításokat a végrehajtás során, hogy segítsen megérteni, hogy mi történik. További részletekért olvassa el ezt a kimenetet.

## <a name="general-usage"></a>Általános használat

A TexConv mindig **pontosan egy kimeneti** fájlt hoz létre. **Több bemeneti** fájlt is használhat a kimenet összeállításához. A szerelvényhez szükség van egy csatorna- **hozzárendelésre**is, amely azt jelzi, hogy melyik csatornán (*vörös, zöld, kék* vagy *alfa*) kell elkészítenie a bemeneti fájlt, és át kell helyeznie azt a kimeneti rendszerkép adott csatornáján.

A legegyenesebb továbbítási parancssor a következő:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`a kimeneti fájl és a formátum megadása
- `-in0`Megadja az első bemeneti képet
- `-rgba`azt jelzi, hogy a kimeneti képnek mind a négy csatornát kell használnia, és a bemeneti rendszerképből 1:1 kell venni.

## <a name="multiple-input-files"></a>Több bemeneti fájl

Ha több bemeneti fájlból kívánja összeállítani a kimenetet, adja meg az összes bemeneti fájlt a `-in` beállítással növekvő számmal:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Cubemap 2D-textúrákból való összegyűjtésekor az egyik a következőt is használhatja:,,,, `-right` `-left` `-top` `-bottom` `-front` `-back` vagy `-px` , `-nx` `-py` `-ny` `-pz` `-nz` ,,,,.

Ha ezeket a bemeneteket a kimeneti fájlba szeretné képezni, megfelelő csatorna-megfeleltetésre van szükség.

## <a name="channel-mappings"></a>Csatorna-hozzárendelések

A csatorna-hozzárendelési beállítások határozzák meg, hogy melyik bemenetből kell kitölteni az adott kimeneti csatornákat. Az egyes csatornák bemeneteit a következőhöz hasonlóan adhatja meg:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Itt a kimenet RGB-csatornái kitöltése az első bemeneti képpel történik, de a vörös és a kék szín cserélve lesz. A kimenet alfa-csatornája a második bemeneti rendszerkép piros csatornájának értékeivel lesz kitöltve.

Az egyes csatornák leképezésének megadása külön biztosítja a legnagyobb rugalmasságot. Az egyszerűség kedvéért a "swizzling" operátorok is megírhatók:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Kimeneti csatornák

A következő csatorna-leképezési lehetőségek érhetők el:

- `-r`, `-g` , `-b` , `-a` : Az egycsatornás hozzárendelések meghatározása
- `-rg`: A vörös és a zöld csatorna hozzárendeléseinek meghatározása.
- `-rgb`: A vörös, zöld és kék csatorna hozzárendeléseinek meghatározása.
- `-rgba`: Megadja mind a négy csatorna hozzárendelését.

A csak az R, RG vagy RGB csatornát említve arra utasítja a TexConv, hogy hozzon létre egy kimeneti fájlt, amely csak 1, 2 vagy 3 csatornával rendelkezik.

### <a name="input-swizzling"></a>Bemeneti swizzling

Amikor megadja, hogy melyik bemeneti textúrának kell kitöltenie a kimeneti csatornát, az egyik csalás a bemenetet:

- `-rgba in0`egyenértékű a következővel`-rgba in0.rgba`
- `-rgba in0.bgra`a bemeneti csatornákat fogja csalás
- `-rgb in0.rrr`a piros csatornát minden csatornára duplikálja

A csatornákat feketével vagy fehérrel is kitöltheti:

- `-rgb in0 -a white`létrehoz egy 4 csatornás kimeneti textúrát, de az alfaot teljesen átlátszatlanra állítja
- `-rg black -b white`egy teljesen kék textúrát fog létrehozni

## <a name="common-options"></a>Gyakori beállítások

A legérdekesebb lehetőségek alább láthatók. A további lehetőségek a következők: `TexConv --help` .

### <a name="output-type"></a>Kimenet típusa

- `-type 2D`: A kimenet normál 2D-képként jelenik meg.
- `-type Cubemap`: A kimenet egy cubemap-rendszerkép lesz. Csak a DDS kimeneti fájlok esetében támogatott. Ha ez meg van adva, a cubemap 6 normál 2D bemeneti rendszerképből is összeállítható.

### <a name="image-compression"></a>Képek tömörítése

- `-compression none`: A kimeneti rendszerkép kibontása nem történik meg.
- `-compression medium`: Ha támogatott, a kimeneti rendszerkép tömörítést fog használni a túl sok minőség feláldozása nélkül.
- `-compression high`: Ha támogatott, a kimeneti rendszerkép tömörítést és az áldozat minőségét fogja használni egy kisebb fájl javára.

### <a name="mipmaps"></a>Mipmap

Alapértelmezés szerint a TexConv a Mipmap hozza létre, ha a kimeneti formátum támogatja azt.

- `-mipmaps none`: A Mipmap nem jön létre.
- `-mipmaps Linear`: Ha támogatott, a Mipmap egy box-szűrő használatával jön létre.

### <a name="usage-srgb--gamma-correction"></a>Használat (sRGB/gamma-javítás)

Ez a `-usage` beállítás határozza meg a kimenet célját, és így közli a TexConv, hogy a bemeneti és a kimeneti fájlok esetében a gamma-korrekciót kell-e alkalmazni. A használat csak az RGB-csatornákat érinti. Az alfa-csatorna mindig "lineáris" értékeket tartalmaz. Ha nincs megadva a használat, az "automatikus" mód megpróbálja észlelni a használatot az első bemeneti rendszerkép formátuma és fájlneve alapján. Például a single és a Dual Channel kimeneti formátum mindig lineáris. Tekintse meg a kimenetet, és tekintse meg, hogy milyen TexConv hozott létre.

- `-usage Linear`: A kimeneti rendszerkép olyan értékeket tartalmaz, amelyek nem képviselnek színeket. Ez általában a fémes és a durva textúrák, valamint a különböző maszkok esetében van.

- `-usage Color`: A kimeneti képképet jelölő szín, például diffúz/albedó térképek. Az sRGB jelző a kimeneti DDS fejlécben lesz beállítva.

- `-usage HDR`: A kimeneti fájlnak a kódoláshoz csatornánként 8 bitesnél több bitet kell használnia. Ennek következtében az összes érték lineáris térben tárolódik. A HDR-textúrák esetében nem számít, hogy az adott szín vagy más adatértéket képvisel-e.

- `-usage NormalMap`: A kimeneti rendszerkép egy tangens – normál Térkép. Az értékek normalizálva lesznek, és a Mipmap számítás kis mértékben lesz optimalizálva.

- `-usage NormalMap_Inverted`: A kimenet egy, a bemenettől eltérő irányba mutató szóköz, amely az Y-vel ellentétes irányban mutat.

### <a name="image-rescaling"></a>Képek átméretezése

- `-minRes 64`: A kimenet minimális felbontását adja meg. Ha a bemeneti rendszerkép kisebb, akkor a rendszer felskálázást kap.
- `-maxRes 1024`: A kimenet maximális felbontását adja meg. Ha a bemeneti rendszerkép nagyobb, akkor a rendszer a downscaled fogja kérni.
- `-downscale 1`: Ha ez meghaladja a 0 értéket, a bemeneti képekre az N-idő felbontása felére kerül. Ezzel általános minőség-csökkentést alkalmazhat.

## <a name="examples"></a>Példák

### <a name="convert-a-color-texture"></a>Színtextúra konvertálása

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Normál Térkép konvertálása

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>HDR-cubemap létrehozása

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

A HDR-cubemaps kiváló forrása a [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Több rendszerkép összesütése egy

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Egyetlen csatorna kibontása

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
