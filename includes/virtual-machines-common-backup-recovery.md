
## <a name="azure-backup"></a>Azure Backup

Termelési számítási feladatokhoz futtató Azure virtuális gépek biztonsági mentéséről, használja az Azure Backup szolgáltatásnál. Azure biztonsági mentés a Windows és a Linux virtuális gépek alkalmazáskonzisztens biztonsági mentést támogatja. Az Azure Backup georedundáns helyreállítás tárolók tárolt helyreállítási pontokat hoz létre. Helyreállításakor a helyreállítási pont, az egész virtuális gép vagy csak adott fájlokat is helyreállíthatja. 

Az egyszerű, gyakorlati megismerkedhet az Azure Backup Azure virtuális gépekhez, tekintse meg az "Azure virtuális gépek biztonsági mentése" a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Azure biztonsági mentési működéséről további információkért lásd: [tervezze meg a virtuális gép biztonsági mentési infrastruktúra az Azure-ban](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Az Azure Site Recovery jelentős katasztrófa esetben megakadályozza a virtuális gépek, amikor egy teljes régió miatt jelentős természeti katasztrófa vagy szélesebb körben szolgáltatáskiesést kimaradás. Azure Site Recovery konfigurálhatja a virtuális gépek, így helyreállíthatja az alkalmazás, függetlenül attól, hogy az egyetlen kattintással perc alatt. Az Ön által választott Azure-régióhoz replikálhatja, a nem párhuzamos régiók korlátozni. 

Az igény szerinti feladatátvételi teszteket, anélkül, hogy befolyásolná a termelési számítási feladatokhoz vagy a folyamatban lévő replikáció futtathatja vész-helyreállítási gyakorlatokat. Hozzon létre helyreállítási terv feladatátvételi és a teljes alkalmazás több virtuális gépeken futó feladat-visszavétel számít. A helyreállítási terv funkció integrálva van az Azure automation-forgatókönyveket.

Elkezdheti által [a virtuális gépek replikálásához](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Felügyelt pillanatképek 

Fejlesztési és tesztelési környezetben pillanatképek adjon meg egy gyors és egyszerű beállítása, a felügyelt lemezeket használó virtuális gépek biztonsági mentéséről. Felügyelt pillanatképet egy csak olvasható, teljes másolata egy felügyelt lemezes. A pillanatképek független a forrás lemez létezik, és új kezelt lemezek újbóli létrehozása a virtuális gépek létrehozásához használható. Azok a lemez használt része alapján számlázzuk. Például ha 64 GB-os kiosztott kapacitását és tényleges használt adatok mérete 10 GB-os felügyelt lemezes pillanatképet hoz létre, pillanatkép alapján számlázzuk csak a 10 GB-os használt adatok méretét.  

Pillanatképeinek további információkért lásd:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>További lépések
Módszer kipróbálásához Azure Backup a "biztonságimásolat-Windows virtuális gépek oktatóanyag" követve a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
