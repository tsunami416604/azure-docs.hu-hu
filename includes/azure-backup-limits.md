
A következő korlátozások vonatkoznak az Azure biztonsági mentés.

| Korlát azonosítója | Alapértelmezett korlát |
| --- | --- |
| Minden egyes tárolóban elleni regisztrálható kiszolgálók/gépek számát |a Windows Server/ügyfél/SCDPM 50 <br/> 200 IaaS virtuális gépekhez |
| Egy adatforrás tároló Azure storage-ban tárolt adatok mérete |54400 GB maximális<sup>1</sup> |
| Mentési tárolók hozható létre az egyes Azure-előfizetések száma |25 (mentési tárolókban) <br/> Régiónként 25 recovery Services-tárolók |
| Ennyiszer naponta biztonsági mentés ütemezése |a Windows Server vagy Windows-ügyfélen naponta 3 <br/> az SCDPM naponta 2 <br/> Infrastruktúra-szolgáltatási virtuális gépek naponta egyszer |
| A biztonsági mentéshez Azure virtuális géphez csatolt adatok lemezek |16 |
| Egy Azure virtuális géphez a biztonsági mentéshez egyedi adatokat lemez mérete| 1023 GB-OS <sup>2</sup>|

* <sup>1</sup>infrastruktúra-szolgáltatási virtuális gép biztonsági mentése nem vonatkozik a 54400 GB-os korlátot.
* <sup>2</sup> tudunk egy [private Preview verziójára](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) nem felügyelt lemezek legfeljebb 4 TB-os támogatásához. 

