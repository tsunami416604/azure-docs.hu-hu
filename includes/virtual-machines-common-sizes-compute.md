<!-- F-series, Fs-series* -->

Fsv2-sorozat alapján Intel® Xeon® Platinum 8168 processzor kiemeli a 2.7 GHz-es alapszintű core gyakoriságát és 3.7 GHz-es maximális egymagos turbo gyakorisága. Intel® AVX-512 utasításokat, amelyek új Intel méretezhető processzoron, akár egy 2 X a vektoros feldolgozási terheléshez egyetlen és a dupla pontosságú lebegőpontos művelet a Teljesítmény program ad meg. Ez azt jelenti azok valóban gyors a számítási munkaterhelés számára. 

Alacsonyabb áron óránként listát a Fsv2-sorozat a értéke ajánlott ár-teljesítmény az Azure összetevőiben, az az Azure számítási egység (ACU) vCPU száma alapján. 

Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  

Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a vCPU-nkénti memória- vagy ideiglenes tárterületigényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.

Az Fs-sorozat az F-sorozat összes előnyét biztosítja a prémium szintű tárterület mellett.

# <a name="fsv2-series"></a>Fsv2-sorozat

ACU: 195-210

| Méret             | vCPU meg | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | Mérsékelt                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | Mérsékelt                                       |
| Standad_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | Magas                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | Magas                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | Rendkívül nagy                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | Rendkívül nagy                                 |
| Standard_F72s_v2 | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | Rendkívül nagy                                 |


## <a name="fs-series"></a>Fs-sorozat*

ACU: 210–250

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4,000 / 32 (12) |3,200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |4 |8,000 / 64 (24) |6,400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |8 |16,000 / 128 (48) |12,800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |16 |32,000 / 256 (96) |25,600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |32 |64,000 / 512 (192) |51,200 / 768 |8 / 6000-12000 &#8224; |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

*Az Fs-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.


<br>

## <a name="f-series"></a>F-sorozat

ACU: 210–250

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / 6000 - 12000 &#8224;           |


<br>


