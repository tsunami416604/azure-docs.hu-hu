---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857398"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Virtuális gépek méretének rugalmassága fenntartott VM-példányokkal

Egy fenntartott virtuálisgép-példánnyal, amely a példányok méretének rugalmasságára van optimalizálva, a megvásárolt foglalás a virtuális gépek (VM-EK) méretére is vonatkozhat az azonos méretű adatsorozat-csoportban. Ha például a DSv2 sorozatú táblában felsorolt virtuálisgép-mérethez (például Standard_DS5_v2) foglal le egy foglalást, akkor a foglalási kedvezmény az ugyanabban a táblában felsorolt négy méretre is vonatkozhat:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

A foglalási kedvezmény azonban nem vonatkozik a különböző táblázatokban felsorolt virtuális gépek méretére, például a DSv2-sorozat nagy memóriájának táblázatára: Standard_DS11_v2, Standard_DS12_v2 stb.

A méret adatsorozat csoportjában a foglalási kedvezményt tartalmazó virtuális gépek száma a foglalás megvásárlásakor kiválasztott virtuálisgép-mérettől függ. Emellett a futtatott virtuális gépek méretétől is függ. Az alábbi táblázatokban felsorolt arány oszlop összehasonlítja az adott csoportban található virtuálisgép-méretek relatív lábnyomát. A Ratio érték használatával kiszámíthatja, hogy a foglalási kedvezmény Hogyan vonatkozik a futtatott virtuális gépekre.

## <a name="examples"></a>Példák

Az alábbi példák a DSv2 sorozatú táblában lévő méreteket és arányokat használják.

 A fenntartott VM-példányok olyan méretű Standard_DS4_v2 vásárolhatnak, ahol az arány vagy a relatív lábnyom a sorozat egyéb méreteihez képest 8.

- forgatókönyv 1: Nyolc Standard_DS1_v2 méretű virtuális gépet futtasson 1 arányban. A foglalási kedvezmény mind a nyolc virtuális gép esetében érvényes.
- 2\. forgatókönyv: Futtasson két, egyenként 2 Standard_DS2_v2 méretű virtuális gépet. Futtasson egy Standard_DS3_v2 méretű virtuális gépet is, amelynek a aránya 4. A teljes helyigény 2 + 2 + 4 = 8. Így a foglalási kedvezmény mindhárom virtuális gépre vonatkozik.
- 3\. forgatókönyv: Futtasson egy Standard_DS5_v2 16-os aránnyal. A foglalási kedvezmény a virtuális gép számítási díja felére vonatkozik.

A következő fejezetek azt mutatják be, hogy az azonos méretű adatsorozat-csoport milyen méreteket tartalmaz, ha olyan fenntartott VM-példányt vásárol, amely a példány méretének rugalmasságára optimalizált

## <a name="b-series"></a>B sorozat

| Size | Arány|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

További információ: [B-sorozatú virtuális gépek mérete](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>B sorozat – nagy memória

| Size | Arány|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

További információ: [B-sorozatú virtuális gépek mérete](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-sorozat

| Size | Arány|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>D sorozat – nagy memória

| Size | Arány|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>DS sorozat

| Size | Arány|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>DS-sorozat – nagy memória

| Size | Arány|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-sorozat

| Size | Arány|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>DSv2 sorozat – nagy memória

| Size | Arány|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>DSv2 nagy memória elkülönített sorozat

| Size | Arány|
|---|---|
|Standard_DS15i_v2|1|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>DSv3 sorozat

| Size | Arány|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

További információ: [általános célú virtuálisgép-méretek](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Dv2-sorozat

| Size | Arány|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Dv2 sorozat – nagy memória

| Size | Arány|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Dv2 nagy memória elkülönített sorozat

| Size | Arány|
|---|---|
|Standard_D15i_v2|1|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Dv3-sorozat

| Size | Arány|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard d32 v3|16|
|Standard_D64_v3|32|

További információ: [általános célú virtuálisgép-méretek](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>ESv3-sorozat

| Size | Arány|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>ESv3 sorozat izolált sorozata

| Size | Arány|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Ev3-sorozat

| Size | Arány|
|---|---|
|Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Ev3 sorozat izolált sorozata

| Size | Arány|
|---|---|
|Standard_E64i_v3|1|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-sorozat

| Size | Arány|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>FS sorozat

| Size | Arány|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

További információ: [a virtuális gépek méreteinek előző generációi](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Fsv2-sorozat

| Size | Arány|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

További információ: [számítási optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>H-sorozat

| Size | Arány|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

További információ: [nagy teljesítményű számítási VM-méretek](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>H-sorozat – nagy memória

| Size | Arány|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

További információ: [nagy teljesítményű számítási VM-méretek](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>HCS FRISSÍTŐÜGYNÖK sorozat

| Size | Arány|
|---|---|
|Standard_HC44rs|1|

További információ: [nagy teljesítményű számítási VM-méretek](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>HBS sorozat

| Size | Arány|
|---|---|
|Standard_HB60rs|1|

További információ: [nagy teljesítményű számítási VM-méretek](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>HBS sorozat

| Size | Arány|
|---|---|
|Standard_HB60rs|1|

További információ: [nagy teljesítményű számítási VM-méretek](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>H sorozat – kis késés – sorozat

| Size | Arány|
|---|---|
|Standard h16r|1|

További információ: a [tárolásra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>H sorozat nagy memória alacsony késésű sorozata

| Size | Arány|
|---|---|
|Standard h16mr|1|

További információ: a [tárolásra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>H-sorozat

| Size | Arány|
|---|---|
|Standard_H8|1|
|Standard_H16|2|

További információ: a [tárolásra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>LSv2 sorozat – sorozat

| Size | Arány|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

További információ: a [tárolásra optimalizált virtuális gépek méretei](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>M sorozat

| Size | Arány|
|---|---|
| Standard m64s|1|
|Standard m128s|2|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Az M sorozat frakcionált

| Size | Arány|
|---|---|
| Standard m16s|1|
|Standard m32s|2|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Az M-sorozat tört nagy memóriája

| Size | Arány|
|---|---|
|Standard m8ms|1|
|Standard_M8 – 2ms|1|
|Standard_M8 – 4ms|1|
|Standard_M16ms|2|
|Standard_M16 – 4ms|2|
|Standard_M16 – 8ms|2|
|Standard m32ms|4|
|Standard_M32 – 8ms|4|
|Standard_M32-16ms|4|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Az M-sorozat részlegesen nagy

| Size | Arány|
|---|---|
|Standard m32ls|1|
|Standard m64ls|2|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>M sorozat – nagy memória

| Size | Arány|
|---|---|
|Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard m128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>MS sorozat (frakcionális, kis méretű)

| Size | Arány|
|---|---|
|Standard m32ls|1|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>MSv2 sorozat – nagy memória sorozat

| Size | Arány|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>MSv2 sorozat

| Size | Arány|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

További információ: a [memória-optimalizált virtuális gépek méretei](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC sorozat

| Size | Arány|
|---|---|
|Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2 sorozat

| Size | Arány|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3 sorozat

| Size | Arány|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND sorozat

| Size | Arány|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV sorozat

| Size | Arány|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

## <a name="nvsv3-series"></a>NVSv3 sorozat

| Size | Arány|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>NDS sorozat – kis késésű sorozat

| Size | Arány|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>NCSv3 sorozat – kis késleltetésű sorozat

| Size | Arány|
|---|---|
|Standard_NC24rs_v3|1|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>NCSv2 sorozat – kis késleltetésű sorozat

| Size | Arány|
|---|---|
|Standard_NC24rs_v2|1|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>NC sorozat – kis késésű sorozat

| Size | Arány|
|---|---|
|Standard nc24r|1|

További információ: GPU-ra [optimalizált virtuálisgép-méretek](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).





