
Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ.  Ez a cikk ismerteti a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati teljesítményt minden méretéhez ennél a csoportosításnál száma. 

Az Azure H-sorozat virtuális gépei a nagy teljesítményű feldolgozásra képes virtuális gépek következő generációja, amelyek olyan magas szintű számítási igényekre lettek kialakítva, mint a molekuláris modellezés vagy a folyadékdinamikai számítások. Ezek a 8 és 16 virtuális processzorral rendelkező virtuális gépek az Intel Haswell E5-2667 V3 processzor technológiára épülnek, és DDR4 memóriával, valamint SSD-alapú ideiglenes tárolóval rendelkeznek. 

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.



## <a name="h-series"></a>H-sorozat

ACU: 290–300

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Lemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32x500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64x500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32x500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64x500 |4  |
| Standard_H16r* |16 |112 |2000 |64 |64x500 |4  |
| Standard_H16mr* |16 |224 |2000 |64 |64x500 |4 |

*MPI-alkalmazások esetében a dedikált RDMA-háttérhálózatot az FDR InfiniBand hálózat biztosítja, amely rendkívül alacsony késést és magas sávszélességet kínál.

<br>



## <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok

ACU: 225

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |32 |32x500 |2 |
| Standard_A9* |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

*MPI-alkalmazások esetében a dedikált RDMA-háttérhálózatot az FDR InfiniBand hálózat biztosítja, amely rendkívül alacsony késést és magas sávszélességet kínál.

<br>



