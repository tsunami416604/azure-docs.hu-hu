| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Előfizetésenként storage-fiókok száma | 200<sup>1</sup> |
| A maximális tárolókapacitás fiók | 500 TiB<sup>2</sup> |
| Blob tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitásokat vagy tárfiók üzenetek maximális száma | Korlátlan |
| A tárfiók / maximális lekérdezési gyakorisága | 20 000 kérelmek / másodperc<sup>2</sup> |
| Maximális érkező<sup>3</sup> tárolási fiókonként (US régió) | Ha a Georedundáns/ZRS 10 GB/s<sup>4</sup> engedélyezve van, az LRS 20 GB/s<sup>2</sup> |
| Maximális kilépő<sup>3</sup> tárolási fiókonként (US régió) | Ha a ZRS RA-GRS vagy GRS 20 GB/s<sup>4</sup> engedélyezve van, az LRS 30 GB/s<sup>2</sup> |
| Maximális érkező<sup>3</sup> tárolási fiókonként (Amerikai Egyesült régiókban) | Ha a Georedundáns/ZRS 5 GB/s<sup>4</sup> engedélyezve van, az LRS 10 GB/s<sup>2</sup> |
| Maximális kilépő<sup>3</sup> tárolási fiókonként (Amerikai Egyesült régiókban) | Ha a ZRS RA-GRS vagy GRS 10 GB/s<sup>4</sup> engedélyezve van, az LRS 15 GB/s<sup>2</sup> |

<sup>1</sup>magában foglalja a Standard és prémium szintű storage-fiókok. Ha több mint 200 tárfiókra van szüksége, nyújtson be egy kérést az [Azure ügyfélszolgálatán](https://azure.microsoft.com/support/faq/) keresztül. Az Azure Storage csapata kiértékeli az Ön vállalkozását, és jóváhagyhat legfeljebb 250 tárfiókot. 

<sup>2</sup> ahhoz, hogy a hirdetett korlátai, a kapacitás, a be-és kilépési és a kérelmek aránya túli nő a szabványos storage-fiókok, ellenőrizze, hogy a kérelmet [Azure támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapat fog tekintse át a kérést, és előfordulhat, hogy hagyja jóvá a magasabb korlátok eseti alapon.

<sup>3</sup> tárfiókonként csak a fiók be-és kilépési korlátok alapján. *Érkező* küldi el a tárfiók (kérelmek) összes adatokra vonatkozik. *Kimenő forgalom* alatt egy tárfiók felől fogadott összes adatot (választ) értjük.  

<sup>4</sup>azure Storage redundancia lehetőségek a következők:
* **RA-GRS**: írásvédett georedundáns tárolás. RA-GRS engedélyezve van, ha a másodlagos hely kimenő forgalom cél megegyeznek az elsődleges hely.
* **Georedundáns**: georedundáns tárolást. 
* **A ZRS**: zónaredundáns tárolás. Csak a blokkblobokhoz érhető el. 
* **LRS**: helyileg redundáns tárolás. 