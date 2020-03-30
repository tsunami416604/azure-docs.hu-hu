---
title: Linuxos virtuális gépek alkalmazáskonzisztens biztonsági mentései
description: Hozzon létre alkalmazáskonzisztens biztonsági másolatokat a Linux os virtuális gépekről az Azure-ba. Ez a cikk ismerteti a parancsfájl-keretrendszer konfigurálása az Azure által üzembe helyezett Linux virtuális gépek biztonsági. Ez a cikk hibaelhárítási információkat is tartalmaz.
ms.reviewer: anuragm
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 36eeb9f63c67a01bf37412101e23be035596de94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173007"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Az Azure Linux virtuális gépek alkalmazáskonzisztens biztonsági mentése

A virtuális gépek biztonsági mentési pillanatképeinek készítésekor az alkalmazás konzisztenciája azt jelenti, hogy az alkalmazások akkor indulnak el, amikor a virtuális gépek a visszaállítás után elindulnak. Képzelheti, hogy az alkalmazások konzisztenciája rendkívül fontos. Annak érdekében, hogy a Linux virtuális gépek alkalmazás konzisztens, használhatja a Linux elő-script és a parancsfájl utáni keretrendszer alkalmazás-konzisztens biztonsági mentések. Az előre szkript és a parancsfájl utáni keretrendszer támogatja az Azure Resource Manager által üzembe helyezett Linux virtuális gépek. Az alkalmazáskonzisztencia parancsfájljai nem támogatják a Service Manager által telepített virtuális gépeket vagy windowsos virtuális gépeket.

## <a name="how-the-framework-works"></a>A keretrendszer működése

A keretrendszer lehetővé teszi az egyéni elő- és parancsfájlok futtatását a virtuális gép pillanatképeinek készítése közben. Az előzetes parancsfájlok közvetlenül a virtuális gép pillanatképének készítése előtt futnak, és a parancsfájlok utáni parancsfájlok közvetlenül a virtuális gép pillanatképének elkészülte után futnak. Az előzetes parancsfájlok és a parancsfájlok utáni parancsfájlok rugalmasságot biztosítanak az alkalmazás és a környezet vezérléséhez, miközben virtuális gép pillanatképeket készít.

Az előzetes parancsfájlok natív alkalmazás API-kat hívnak meg, amelyek lehalkítják az IOs-t, és a memóriában lévő tartalmat a lemezre ürítik. Ezek a műveletek biztosítják, hogy a pillanatkép konzisztens legyen. A parancsfájlok utáni parancsfájlok natív alkalmazás API-kat használnak az IOs felolvasztására, amelyek lehetővé teszik, hogy az alkalmazás a virtuális gép pillanatképe után folytassa a normál működést.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Az elő- és parancsfájl utáni konfigurálás lépései

1. Jelentkezzen be, mint a root felhasználó a Linux virtuális gép, amely szeretne biztonsági másolatot.

2. A [GitHubról](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)töltse le **a VMSnapshotScriptPluginConfig.json fájlt,** és másolja az **/etc/azure** mappába az összes biztonsági másolatot készíteni kívánt virtuális géphez. Ha az **/etc/azure** mappa nem létezik, hozza létre.

3. Másolja az alkalmazás elő- és utóparancsfájlját az összes olyan virtuális gépre, amelyről biztonsági másolatot szeretne készíteni. A parancsfájlok a virtuális gép bármely helyére másolhatók. Mindenképpen frissítse a parancsfájlfájlok teljes elérési útját a **VMSnapshotScriptPluginConfig.json** fájlban.

4. Győződjön meg arról, hogy a következő engedélyek szükségesek ezekhez a fájlokhoz:

   - **VMSnapshotScriptPluginConfig.json**: Engedély "600." Például csak a "root" felhasználónak kell "olvasási" és "írási" engedéllyel rendelkeznie ehhez a fájlhoz, és egyetlen felhasználónak sem szabad "végrehajtási" engedélyekkel rendelkeznie.

   - **Elő-script fájl**: Engedély "700."  Például csak a "root" felhasználónak kell "olvasni", "írás" és "végrehajtási" engedéllyel ehhez a fájlhoz.

   - **Utóirat** Engedély "700.". Például csak a "root" felhasználónak kell "olvasni", "írás" és "végrehajtási" engedéllyel ehhez a fájlhoz.

   > [!IMPORTANT]
   > A keret rendszer ad használók sok erő. Biztosítsa a keretrendszert, és győződjön meg arról, hogy csak a "root" felhasználó rendelkezik hozzáféréssel a kritikus JSON és parancsfájlok.
   > Ha a követelmények nem teljesülnek, a parancsfájl nem fog futni, ami a fájlrendszer összeomlását és inkonzisztens biztonsági mentést eredményez.
   >

5. Konfigurálja **a VMSnapshotScriptPluginConfig.json** t az itt leírtak szerint:
    - **pluginName**: Hagyja meg ezt a mezőt úgy, ahogy van, vagy előfordulhat, hogy a parancsfájlok nem a várt módon működnek.

    - **preScriptLocation**: Adja meg az elő-parancsfájl teljes elérési útját a virtuális gépen, amely biztonsági másolatot fog kapni.

    - **postScriptLocation**: Adja meg a teljes elérési útját a post-script a virtuális gép, amely lesz biztonsági másolatot.

    - **preScriptParams**: Adja meg a választható paramétereket, amelyeket át kell adni az előszkriptnek. Minden paraméternek idézőjelben kell lennie. Ha több paramétert használ, válassza el a paramétereket vesszővel.

    - **postScriptParams**: Adja meg a választható paramétereket, amelyeket át kell adni a post-script. Minden paraméternek idézőjelben kell lennie. Ha több paramétert használ, válassza el a paramétereket vesszővel.

    - **preScriptNoOfRetries**: Beállíthatja, hogy az előparancsfájlt hányszor kell újra megpróbálni, ha bármilyen hiba van a megszüntetés előtt. A nulla azt jelenti, hogy csak egy próbálkozás, és nincs újrapróbálkozás, ha hiba van.

    - **postScriptNoOfRetries**: Beállíthatja, hogy a parancsfájl utáni parancsfájlt hányszor kell újra megpróbálni, ha bármilyen hiba van a megszüntetés előtt. A nulla azt jelenti, hogy csak egy próbálkozás, és nincs újrapróbálkozás, ha hiba van.

    - **timeoutInSeconds**: Adja meg az egyes időtúlműveleteket az elő- és a parancsfájl utáni parancsfájlhoz (a maximális érték 1800 lehet).

    - **continueBackupOnFailure:** Állítsa ezt az értéket **igaz** értékre, ha azt szeretné, hogy az Azure Backup egy fájlrendszerkonzisztens/összeomlás konzisztens biztonsági mentésre essen vissza, ha az elő-parancsfájl vagy a parancsfájl utáni parancsfájl sikertelen. Ha ezt **a hamis** beállítást hamisra állítja, a biztonsági mentés parancsfájlhiba esetén sikertelen (kivéve, ha egylemezes virtuális gépe van, amely ettől a beállítástól függetlenül visszaáll az összeomlás-konzisztens biztonsági mentésre).

    - **fsFreezeEnabled**: Adja meg, hogy a Linux fsfreeze kell hívni, miközben a virtuális gép pillanatkép a fájlrendszer konzisztenciájának biztosítása érdekében. Azt javasoljuk, hogy tartsa ezt a beállítást **igaz** értékre, kivéve, ha az alkalmazás függ az fsfreeze letiltását.

    - **ScriptsExecutionPollTimeSeconds**: Állítsa be, hogy a bővítmény nek mikor kell aludnia az egyes lekérdezések között a parancsfájl végrehajtására. Ha például az érték 2, a bővítmény ellenőrzi, hogy az elő-/post script végrehajtás befejeződött-e 2 másodpercenként. A minimális és maximális érték, amelyet elérhet, 1, illetve 5. Az értéknek szigorúan egész számnak kell lennie.

6. A parancsfájl-keretrendszer most konfigurálva van. Ha a virtuális gép biztonsági mentése már konfigurálva van, a következő biztonsági mentés meghívja a parancsfájlokat, és elindítja az alkalmazáskonzisztens biztonsági mentést. Ha a virtuális gép biztonsági mentése nincs konfigurálva, konfigurálja az [Azure virtuális gépek biztonsági mentése a Recovery Services-tárolók használatával.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Hibaelhárítás

Győződjön meg arról, hogy az elő- és parancsfájlírás során adja meg a megfelelő naplózást, és tekintse át a parancsfájlnaplókat a parancsfájlokkal kapcsolatos problémák megoldásához. Ha továbbra is problémái vannak a parancsfájlok futtatásával, további információt az alábbi táblázatban talál.

| Hiba | Hibaüzenet | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Parancsfájl előttivégrehajtássikertelen |Az előparancsfájl hibát adott vissza, így előfordulhat, hogy a biztonsági mentés nem alkalmazható.| A probléma megoldásához tekintse meg a parancsfájl hibanaplóit.|  
|Post-ScriptExecutionFailed |A parancsfájl utáni hiba, amely hatással lehet az alkalmazás állapotát. |Tekintse meg a parancsfájl hibanaplóit a probléma megoldásához és az alkalmazás állapotának ellenőrzéséhez. |
| Elő-ScriptNemFound |Az előparancsfájl nem található a **VMSnapshotScriptPluginConfig.json** konfigurációs fájlban megadott helyen. |Győződjön meg arról, hogy az elő-parancsfájl a konfigurációs fájlban megadott elérési úton található az alkalmazáskonzisztens biztonsági mentés biztosítása érdekében.|
| Post-ScriptNem Alapítva |A post-script nem található a **VMSnapshotScriptPluginPluginConfig.json** konfigurációs fájlban megadott helyen. |Győződjön meg arról, hogy a parancsfájl utáni jelen van a konfigurációs fájlban megadott elérési úton az alkalmazáskonzisztens biztonsági mentés biztosítása érdekében.|
| IncorrectPluginhostFile fájl |A **Pluginhost** fájl, amely a VmSnapshotLinux kiterjesztéssel érkezik, sérült, így az előre szkript és a parancsfájl utáni nem futtatható, és a biztonsági mentés nem lesz alkalmazás-konzisztens.| Távolítsa el a **VmSnapshotLinux** kiterjesztést, és a következő biztonsági mentéssel automatikusan újratelepíti a probléma megoldásához. |
| HelytelenJSONConfigFile | A **VMSnapshotScriptPluginConfig.json** fájl helytelen, így az elő- és utóparancsfájl nem futtatható, és a biztonsági mentés nem lesz alkalmazáskonzisztens. | Töltse le a másolatot a [GitHubról,](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) és konfigurálja újra. |
| InsufficientPermissionfor Pre-Script | A parancsfájlok futtatásához a "root" felhasználónak kell a fájl tulajdonosának lennie, és a fájlnak "700" engedélyekkel kell rendelkeznie (azaz csak a "tulajdonos" rendelkezhet "olvasási", "írási" és "végrehajtási" engedélyekkel). | Győződjön meg arról, hogy a "root" felhasználó a parancsfájl "tulajdonosa", és hogy csak a "tulajdonos" rendelkezik "olvasási", "írási" és "végrehajtási" engedélyekkel. |
| InsufficientPermissionfor Post-Script | A parancsfájlok futtatásához a gyökérfelhasználónak kell a fájl tulajdonosának lennie, és a fájlnak "700" engedélyekkel kell rendelkeznie (azaz csak a "tulajdonosnak" kell "olvasni", "írás" és "végrehajtás" engedélyekkel rendelkeznie). | Győződjön meg arról, hogy a "root" felhasználó a parancsfájl "tulajdonosa", és hogy csak a "tulajdonos" rendelkezik "olvasási", "írási" és "végrehajtási" engedélyekkel. |
| Parancsfájl előttiidő-out | Az alkalmazáskonzisztens biztonsági mentés parancsfájl előtti időbeli végrehajtása. | Ellenőrizze a parancsfájlt, és növelje az időtúltöltést az **/etc/azure**helyen található **VMSnapshotScriptPluginConfig.json** fájlban. |
| Post-ScriptTimeout | Az alkalmazáskonzisztens biztonsági mentés parancsfájl utáni végrehajtása időbeli elévül. | Ellenőrizze a parancsfájlt, és növelje az időtúltöltést az **/etc/azure**helyen található **VMSnapshotScriptPluginConfig.json** fájlban. |

## <a name="next-steps"></a>További lépések

[Virtuális gép biztonsági mentésének konfigurálása a Recovery Services-tárolóba](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
