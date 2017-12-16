
GPU optimalizált VM értékek speciális virtuális gépek egy vagy több NVIDIA Feldolgozóegységekkel érhető el. Ezek méretek számítási igényű grafikai igényű és a képi megjelenítés munkaterhelések készültek. Ez a cikk tájékoztatást ad azokról a számát és típusát Feldolgozóegységekkel, Vcpu, adatlemezek, és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség megadása ennél a csoportosításnál minden méretét. 

* **Hálózati vezérlő által NCv2 és ND** méretek számítási igényű és hálózati igényű alkalmazásokat és algoritmusok, például CUDA - és OpenCL-alapú alkalmazások és szimulációja, AI és részletes tanulási vannak optimalizálva. 
* **Portok HV** méretek optimalizált és távoli képi megjelenítés adatfolyam-, játék, kódolás és keretrendszerek, például a OpenGL és DirectX okhoz VDI-forgatókönyvek készült.  


## <a name="nc-instances"></a>NC-példányok

A hálózati vezérlő által példányok szerint vannak kapcsolva a [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) kártya. Felhasználók is gyorsabb adatok között, ami CUDA energia feltárása alkalmazások crunch, szimulációja összeomlás, ray követett megjelenítési, mély tanulási és egyebek. A NC24r konfiguráció egy kis késleltetésű számítástechnikai munkaterhelések szorosan összekapcsolt párhuzamos optimalizálva nagy átviteli hálózati adapter biztosít.


| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek maximális száma |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis

## <a name="ncv2-instances"></a>NCv2 példányok

NCv2 példányai is a hálózati vezérlő által-sorozat gépek technológiával következő generációja [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) Feldolgozóegységekkel. Ezek Feldolgozóegységekkel biztosít több, mint a jelenlegi NC-sorozat a számítási teljesítmény x 2. Az ügyfelek kihasználhatja a frissített Feldolgozóegységekkel hagyományos HPC munkaterhelések, például tározó modellezési DNS műveleti sorrend, fehérje elemzés, Monte Carlo szimulációja vagy mások számára. A hálózati vezérlő által-sorozat, például a NCv2-sorozat egy konfigurációs kínál, kisebb késést, a számítási munkaterhelés szorosan összekapcsolt párhuzamos optimalizált nagy átviteli hálózati illesztő.

> [!IMPORTANT]
> A méret termékcsalád az előfizetéshez (core) vCPU kvóta kezdetben 0 értékre van állítva minden régióban. [A vCPU kvóta növelését](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [rendelkezésre álló terület](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek maximális száma |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24r_v2 * |24 |1448 | 1344 | 4 | 32 |

1 GPU = egy P100 kártya.

*RDMA-kompatibilis

## <a name="nd-instances"></a>NT-példányok

NT-sorozatú virtuális gépek a GPU termékcsalád AI és részletes tanulási munkaterhelések készült új mellett. Képzés és megállapítás kiváló teljesítményt biztosítanak. NT-példányok szerint vannak kapcsolva [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Feldolgozóegységekkel. Ezek a példányok egyszeres pontosságú lebegőpontos művelet, Microsoft kognitív eszközkészlet, TensorFlow, Caffe és egyéb keretrendszerekre okhoz AI munkaterhelések kiváló teljesítményt nyújtanak. Az ND sorozat jóval nagyobb GPU-memóriával rendelkezik (24 GB), így jelentősen nagyobb neurálishálózat-modellekhez is alkalmazható. A hálózati vezérlő által-sorozat, például az NT-sorozat kínál a konfiguráció az RDMA, a másodlagos alacsony késésű, nagy átviteli hálózaton és InfiniBand-kapcsolatokat, sok Feldolgozóegységekkel átfedés nagyméretű képzési feladatok futtatása.

> [!IMPORTANT]
> A méret termékcsalád az előfizetésében régiónként vCPU (core) kvóta kezdetben 0 értékre van állítva. [A vCPU kvóta növelését](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [rendelkezésre álló terület](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek maximális száma |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6 |6 |112 | 336 | 1 | 12 |
| Standard_ND12 |12 |224 | 672 | 2 | 24 |
| Standard_ND24 |24 |448 | 1344 | 4 | 32 |
| Standard_ND24r * |24 |1448 | 1344 | 4 | 32 |

1 GPU = egy P40 kártya.

*RDMA-kompatibilis

## <a name="nv-instances"></a>NV-példányok



A portok HV-példányok szerint vannak kapcsolva [NVIDIA Tesla M60 ](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) Feldolgozóegységekkel és NVIDIA rács technológia asztali az elérését gyorsítja fel, alkalmazások és virtuális asztalok ahol az ügyfelek képesek jelenítheti meg az adatok vagy szimulációja. Felhasználók is megjelenítheti a grafikus intenzív munkafolyamatok a felső szintű grafikus képességet, és emellett a kódolás és megjelenítési például az egyszeres pontosságú feladatokat futtató portok HV-példányokon. 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek maximális száma |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = fél M60 kártya.


