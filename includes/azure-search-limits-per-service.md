A tárhelyet a lemezterület mérete, illetve az indexek vagy dokumentumok *maximális számának* rögzített korlátja korlátozza, attól függően, melyik teljesül előbb.

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Szolgáltatói szerződés (SLA) |No <sup>1</sup> |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partíciók szolgáltatásonként |– |1 |12 |12 |12 |3 <sup>2</sup> |
| Partíció mérete |– |2 GB |25 GB |100 GB |200 GB |200 GB |
| Replikák |– |3 |12 |12 |12 |12 |
| Indexek maximális száma |3 |5 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként | 
| Indexelők maximális száma |3 |5 |50 |200 |200 |Az indexelők nem támogatottak | 
| Adatforrások maximális száma |3 |5 |50 |200 |200 |Az indexelők nem támogatottak | 
| Dokumentumok maximális száma |10,000 |1 millió |15 millió partíciónként vagy 180 millió szolgáltatásonként |60 millió partíciónként vagy 720 millió szolgáltatásonként |120 millió partíciónként vagy 1,4 milliárd szolgáltatásonként |1 millió indexenként vagy 200 millió partíciónként | 

<sup>1</sup> ingyenes szint és az előzetes funkciók nem tartoznak a szolgáltatásszint-szerződések (SLA). Az összes számlázható rétegek SLA-k érvénybe, ha a szolgáltatás megfelelő redundancia kiépítése. Két vagy több replikák szükségesek (olvasás) lekérdezés SLA-t. Három vagy több replikák lekérdezés és az indexelés (írásra) SLA szükségesek. A partíciók számának nincs egy SLA-t szempont. 

<sup>2</sup> Az S3 HD három partíciós rögzített korláttal rendelkezik, amely alacsonyabb, mint az S3 partíciókorlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

