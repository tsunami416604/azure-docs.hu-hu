
GPU optimalizált VM értékek nagy mennyiségű grafikus megjelenítési és videó szerkesztése szánt speciális virtuális gépekhez. Egy vagy több Feldolgozóegységekkel érhető el. Ez a cikk tájékoztatást ad azokról a Vcpu, adatlemezek, és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati teljesítményt minden méretéhez ennél a csoportosításnál száma. 


Az NC- és NV-méretek más néven GPU-kompatibilis példányokként is ismertek. A különböző alkalmazási helyzetek és a használati esetek optimalizált NVIDIA GPU-kártyákkal tartalmazó speciális virtuálisgép-méretek. Az NV-méretek OpenGL, DirectX és hasonló keretrendszereket használó távoli megjelenítési, streamelési, játék-, kódolási és VDI-forgatókönyvekhez lettek kialakítva és optimalizálva. Az NC-méretek inkább nagy számítási és hálózatigényű alkalmazásokra és algoritmusokra vannak optimalizálva, beleértve a CUDA- és OpenCL-alapú alkalmazásokat és szimulációkat. 


A gyorsított asztali alkalmazások és virtuális asztalok esetében az NVIDIA által gyártott Tesla M60 GPU kártya és az NVIDIA GRID működteti az NV-példányokat, így az ügyfelek hatékonyan jeleníthetik meg adataikat vagy szimulációikat. Felhasználók is megjelenítheti a grafikus intenzív munkafolyamatok a felső szintű grafikus képességet, és emellett a kódolás és megjelenítési például az egyszeres pontosságú feladatokat futtató portok HV-példányokon. A Tesla M60 két grafikus kártyát tartalmazó kialakítása 4096 CUDA-magot biztosít akár 36 egyidejű 1080p H.264 kódolású streammel. 

Az NC-példányok az NVIDIA által gyártott Tesla K80 kártyát használják. A felhasználók a CUDA kiaknázásával sokkal gyorsabban dolgozhatják fel az adatokat az energiafeltérképező alkalmazások, az ütközésszimulációk, a sugárkövetéses renderelés, a mély tanulás és egyebek terén. A Tesla K80 két grafikai kártyát tartalmazó kialakítása 4992 CUDA-magot biztosít, akár 2,91 Teraflop kétszeres pontosságú vagy 8,93 Teraflop egyszeres pontosságú teljesítménnyel.

## <a name="nv-instances"></a>NV-példányok

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek maximális száma |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = fél M60 kártya.

## <a name="nc-instances"></a>NC-példányok

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek maximális száma |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis


