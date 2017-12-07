 



Az Azure-alapú számítási egység (Azure Compute Unit, ACU) fogalmát azért vezettük be, hogy lehetséges legyen összehasonlítani az egyes Azure-termékváltozatok számítási (CPU-) teljesítményét. Így könnyebben választhatja ki a teljesítményigényeinek leginkább megfelelő termékváltozatot.  Az ACU jelenlegi standard alapjaként a Kisméretű (Standard_A1) virtuális gép 100-as értéket képvisel, és a többi termékváltozat értéke ehhez képest jelöli, hogy mennyivel gyorsabban futtatja az adott termékváltozat a standard teljesítménytesztet. 

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál.  Az egyes számítási terhelések eredményei ettől eltérhetnek. 
> 
> 

<br>

| Termékváltozat-család | ACU \ vCPU | vCPU:Core |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1–A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5–A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2–A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2–A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1–D14](../articles/virtual-machines/windows/sizes-general.md) |160 | 1:1 |
| [D1_v2–D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS1–DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 | 1:1 |
| [DS1_v2–DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1** |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1** |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1** |
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1** |
| [F2s_v2-F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195-210* | 2:1** |
| [F1-F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [F1s-F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [G1–G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [GS1–GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* | 1:1 |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* | 1:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160-180 | 2:1** |

A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt.  A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

**Többszálú. 
