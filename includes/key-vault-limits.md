Kulcs tranzakciók (10 másodperc telepítésenként engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup>):

|Kulcstípus|HSM-Key<br>Kulcs létrehozása|HSM-kulcs<br>Minden más tranzakciók|Szoftver-kulcs<br>Kulcs létrehozása|Szoftver-kulcs<br>Minden más tranzakciók|
|:---|---:|---:|---:|---:|
|2048 bites RSA|5|1000|10|2000|
|3072 bites RSA|5|250|10|500|
|RSA 4096 bites|5|125|10|250|
|

Titkos kulcsok, a Tárfiókkulcsok felügyelt és a tároló tranzakciók:
| Tranzakciók típusa | 10 másodperc telepítésenként engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |
|

Lásd: [szabályozás útmutató az Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) kapcsolatos információk kezeléséhez, ha túllépik szabályozás.

<sup>1</sup> előfizetés kiterjedő maximális minden tranzakció esetében, amely kulcstároló korlátja 5 x. Ha például HSM - előfizetésenként más tranzakciók korlátozva, és 5000 tranzakciók előfizetésenként 10 másodperc.
