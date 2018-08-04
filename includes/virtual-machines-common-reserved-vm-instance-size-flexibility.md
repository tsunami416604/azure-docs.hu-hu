---
author: manish-shukla01
ms.author: manshuk
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-03-2018
ms.openlocfilehash: d8e749d7c665bcaef9190bcc572e9552a307aa08
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39496208"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Virtuális gép mérete rugalmasan fenntartott VM-példányok

A fenntartott virtuálisgép-példányt, amely optimalizált rendelkezik példány mérete rugalmasan a foglalást vásárolhat az azonos méretű adatsorozat-csoport a virtuális gépek (VM)-méretek is alkalmazhat. Például ha vásárol egy foglalást egy Virtuálisgép-méret, amely szerepel a DSv2-sorozat tábla Standard_DS5_v2, például a foglalási kedvezményt is alkalmazhatja a további négy méretek ugyanabban a táblában szereplő:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

A foglalási kedvezményt a különböző táblák, például, hogy mi az a DSv2-sorozat magas memóriahasználat táblázatban felsorolt virtuálisgép-méretek nem vonatkozik, de: Standard_DS11_v2, Standard_DS12_v2, és így tovább.

A méretcsoport sorozat virtuális gépek, alkalmazza a foglalási kedvezményt a virtuális gép méretét, válasszon ki egy fenntartott példányok vásárlásakor függ. Attól is függ, a méret a virtuális gépek, amely rendelkezik futó. Az arány oszlop az alábbi táblázatban szereplő minden egyes Virtuálisgép-méret, hogy a csoportban a relatív erőforrás-igényű hasonlítja össze. Használat alapján számítja ki, hogyan a foglalási kedvezményt vonatkozik a virtuális gépek, az arány értéknek futtatása.

## <a name="examples"></a>Példák

Az alábbi példák a méretek és arányok használata a DSv2-sorozat tábla.

 A fenntartott VM-példány méretének hol az arány vagy az a sorozat további méretek képest relatív erőforrás-igényű 8 Standard_DS4_v2 vásárolhat.

- Standard_ds1_v2 méret 1. forgatókönyv: Nyolc futtatása az 1-es méretű virtuális gépeken. A foglalási kedvezményt a virtuális gépek összes nyolc vonatkozik.
- Standard_DS2_v2 2. példa: Két futtatása az egyes 2-es méretű virtuális gépeken. Egy Standard_DS3_v2 is futtathatja a 4-es méretű virtuális Gépet. A rendszer a teljes erőforrás-igényű 2 + 2 + 4 = 8. Ezért a foglalási kedvezményt mind a három virtuális gépek vonatkozik.
- 3. forgatókönyv: Egy Standard_DS5_v2 16 arányú futtassa. A foglalási kedvezményt a virtuális gép fele a számítási költségek vonatkozik.

A következő szakaszok show, milyen méretek a következők az azonos méretű adatsorozat-csoport a fenntartott VM-példány vásárlásakor optimalizált példány mérete rugalmasságot.

## <a name="b-series"></a>B sorozat

| Méret | Arány|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

További információkért lásd: [a B sorozat – adatlöket-kezelés virtuálisgép-méretek](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Magas memória – B sorozat

| Méret | Arány|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

További információkért lásd: [a B sorozat – adatlöket-kezelés virtuálisgép-méretek](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-sorozat

| Méret | Arány|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

További információkért lásd: [virtuálisgép-méretek előző generációs szoftvereknél jobban](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Magas memória – D sorozat

| Méret | Arány|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

További információkért lásd: [virtuálisgép-méretek előző generációs szoftvereknél jobban](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>DS-sorozat

| Méret | Arány|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

További információkért lásd: [virtuálisgép-méretek előző generációs szoftvereknél jobban](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>DS-sorozat magas memóriahasználat

| Méret | Arány|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

További információkért lásd: [virtuálisgép-méretek előző generációs szoftvereknél jobban](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-sorozat

| Méret | Arány|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

További információkért lásd: [általános célú virtuális gépek méretei](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dsv2-series-high-memory"></a>DSv2 sorozat magas memóriahasználat

| Méret | Arány|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS12_v2|2|
|Standard_DS13_v2|4|
|Standard_DS14_v2|8|
|Standard_DS15_v2|10|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#dsv2-series-11-15).

## <a name="dsv3-series"></a>A DSv3-sorozat

| Méret | Arány|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

További információkért lásd: [általános célú virtuális gépek méretei](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup).

## <a name="dv2-series"></a>Dv2-sorozat

| Méret | Arány|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

További információkért lásd: [általános célú virtuális gépek méretei](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dv2-series-high-memory"></a>A Dv2 sorozat magas memóriahasználat

| Méret | Arány|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#dv2-series-11-15).

## <a name="dv3-series"></a>Dv3-sorozat

| Méret | Arány|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard d32 v3|16|
|Standard D64 v3|32|

További információkért lásd: [általános célú virtuális gépek méretei](../articles/virtual-machines/windows/sizes-general.md#dv3-series-sup1sup).

## <a name="esv3-series"></a>ESv3-sorozat

| Méret | Arány|
|---|---|
| Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E8s_v3|4|
|Standard_E16s_v3|8|
|Standard_E32s_v3|16|
|Standard_E64s_v3|32|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Ev3-sorozat

| Méret | Arány|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-sorozat

| Méret | Arány|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

További információkért lásd: [számításra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-compute.md#f-series).

## <a name="fs-series"></a>FS-sorozat

| Méret | Arány|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

További információkért lásd: [számításra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-compute.md#fs-series-sup1sup).

## <a name="fsv2-series"></a>Fsv2-sorozat

| Méret | Arány|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

További információkért lásd: [számításra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-sup1sup).

## <a name="h-series"></a>H-sorozat

| Méret | Arány|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

További információkért lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Magas memória H-sorozat

| Méret | Arány|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

További információkért lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Ls-sorozat

| Méret | Arány|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

További információkért lásd: [tárolásra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>M sorozat

| Méret | Arány|
|---|---|
| Standard m64s|1|
|Standard m 128 s|2|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>M-sorozat tört

| Méret | Arány|
|---|---|
| Standard m16s|1|
|Standard m32s|2|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>M-sorozat tört magas memóriahasználat

| Méret | Arány|
|---|---|
| Standard m8ms|1|
|Standard m16ms|2|
|Standard m32ms|4|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>M-sorozat tört nagy

| Méret | Arány|
|---|---|
| Standard m32ls|1|
|Standard m64ls|2|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>M-sorozat magas memóriahasználat

| Méret | Arány|
|---|---|
| Standard_M64ms|1|
|Standard m 128 MS|2|

További információkért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC sorozat

| Méret | Arány|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

További információkért lásd: [GPU-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2 sorozat

| Méret | Arány|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

További információkért lásd: [GPU-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3 sorozat

| Méret | Arány|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

További információkért lásd: [GPU-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND sorozat

| Méret | Arány|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

További információkért lásd: [GPU-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV sorozat

| Méret | Arány|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

További információkért lásd: [GPU-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


