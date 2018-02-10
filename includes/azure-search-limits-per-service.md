A tárhelyet a lemezterület mérete, illetve az indexek vagy dokumentumok *maximális számának* rögzített korlátja korlátozza, attól függően, melyik teljesül előbb.

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Szolgáltatói szerződés (SLA) |No <sup>1</sup> |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partíciók szolgáltatásonként |– |1 |12 |12 |12 |3 <sup>2</sup> |
| Partíció mérete |– |2 GB |25 GB |100 GB |200 GB |200 GB |
| Replikák |– |3 |12 |12 |12 |12 |
| Indexek maximális száma |3 |5 <sup>3</sup>|50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |
| Indexelők maximális száma |3 |5 <sup>3</sup>|50 |200 |200 |Az indexelők nem támogatottak |
| Adatforrások maximális száma |3 |5 <sup>3</sup>|50 |200 |200 |Az indexelők nem támogatottak |
| Maximális dokumentumok <sup>3</sup> |10,000 |1 millió |15 millió partíciónként vagy 180 millió szolgáltatásonként |60 millió partíciónként vagy 720 millió szolgáltatásonként |120 millió partíciónként vagy 1,4 milliárd szolgáltatásonként |1 millió indexenként vagy 200 millió partíciónként |

<sup>1</sup> ingyenes szint és az előzetes funkciók nem tartoznak a szolgáltatásszint-szerződések (SLA). Az összes számlázható rétegek SLA-k érvénybe, ha a szolgáltatás megfelelő redundancia kiépítése. Két vagy több replikák szükségesek (olvasás) lekérdezés SLA-t. Három vagy több replikák lekérdezés és az indexelés (írásra) SLA szükségesek. A partíciók számának nincs egy SLA-t szempont. 

<sup>2</sup> Az S3 HD három partíciós rögzített korláttal rendelkezik, amely alacsonyabb, mint az S3 partíciókorlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

>[!Important]
> **<sup>3</sup>**  késői 2017 kezdve újonnan létrehozott Azure Search szolgáltatás kiépített használata sokkal hatékonyabb alapul szolgáló hardver-konfigurációkat az egyes régiókban (Dél-Brazília, Kanada központi módosítani kell néhány korlátot Közép-Indiában, USA keleti régiója, Észak USA középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsiában, Egyesült Királyság déli régiója, Nyugat-Európában és USA nyugati régiója):
>
>* Basic és Standard szint keresési szolgáltatások után késői 2017 nem rendelkezik semmilyen határnak; a dokumentumok számát létrehozva csak a tárolási korlátokat ezeket a szolgáltatásokat a érvényesek lesznek. 
>* S3 nagy sűrűségű szolgáltatások késői 2017 után létrehozott a 200 millió dokumentumot partíciónként el lett távolítva, azonban az 1 millió dokumentumot index korlát marad.
>* Késői 2017 után létrehozott alapvető szolgáltatások megnövekedett legfeljebb 15 indexek, az adatforrások és az indexelők rendelkeznek.
>
>Megtudhatja, hogy több kapcsolatos mely korlátok alkalmazása egy meglévő szolgáltatást, az Azure portál segítségével korlát tekintse meg a szolgáltatás áttekintése lapon.