---
title: 'Az Azure Backup: Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentést'
description: Hozzon létre alkalmazáskonzisztens biztonsági mentést a Linux rendszerű virtuális gépek az Azure-bA. Ez a cikk ismerteti az Azure által üzembe helyezett Linuxos virtuális gépek biztonsági mentése szkript keretében konfigurálása. A cikk emellett tartalmaz hibaelhárítási információkat.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: alkalmazáskonzisztens biztonsági mentése; Azure virtuális gép alkalmazáskonzisztens biztonsági mentés; Linux rendszerű virtuális gép biztonsági mentése; Az Azure Backup
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581855"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Az Azure Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentés

A virtuális gépek biztonsági mentési pillanatképek készítése, ha alkalmazáskonzisztencia azt jelenti, az alkalmazások akkor kezdődik, amikor a virtuális gépek rendszerindítójaként után folyamatban van. Belegondolunk, alkalmazáskonzisztencia rendkívül fontos. Győződjön meg, hogy a Linux rendszerű virtuális gépek alkalmazáskonzisztens, használhatja a Linux szkript előtti és utáni keretrendszerrel alkalmazáskonzisztens biztonsági mentés időpontjának is. A szkript előtti és utáni keretrendszerrel Azure Resource Managerrel üzembe helyezett Linuxos virtuális gépeket is támogatja. Parancsfájl-alkalmazás-konzisztencia a Service Manager által telepített virtuális gépek vagy a Windows virtuális gépek nem támogatják.

## <a name="how-the-framework-works"></a>A keretrendszer működése

A keretrendszer egyéni előtti parancsfájlok futtatása lehetőséget biztosít, és utáni parancsfájlok, amíg van véve a virtuális gép pillanatképeket. Üzem előtti parancsfájlok futtatása csak a VM-pillanatkép, és a Futtatás után azonnal, akkor a VM-pillanatkép készítése utáni parancsfájlok végrehajtása előtt. Üzem előtti és utáni parancsfájlok megadása a rugalmasságot az alkalmazás és a környezet, amíg van véve a virtuális gép pillanatképeket.

Üzem előtti parancsfájlok meghívása natív alkalmazás API-k, mely fokozatosan leválasztani az IOS-es és a memórián belüli tartalom ürítése a lemezre. Ezek a műveletek ellenőrizze, hogy a pillanatkép-e alkalmazáskonzisztens. Utólagos parancsfájljainak natív alkalmazás API-k segítségével az IOS-es, amely lehetővé teszi az alkalmazás a szokásos működés folytatásához a VM-pillanatkép utáni olvasztása.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Szkript előtti és utáni konfigurálásának lépései

1. Jelentkezzen be gyökér szintű felhasználóként a Linux rendszerű virtuális gépre, amelyet szeretne készíteni.

2. A [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), töltse le **VMSnapshotScriptPluginConfig.json** , és másolja azt a **/etc/azure** mappában az összes virtuális gép biztonsági. Ha a **/etc/azure** mappa nem létezik, hozza létre.

3. A szkript előtti és utáni az alkalmazás az összes virtuális gépet, készítsen biztonsági másolatot szeretne másolni. A parancsfájlok tetszőleges helyre a virtuális gép másolhatja. Ne felejtse el frissíteni a parancsfájl lévő fájlok teljes elérési útja a **VMSnapshotScriptPluginConfig.json** fájlt.

4. Ellenőrizze, hogy ezek a fájlok az alábbi engedélyeket:

   - **VMSnapshotScriptPluginConfig.json**: Engedély "600." Ha például csak a "root" felhasználó "olvasási" és "olvasási" engedéllyel kell rendelkeznie a fájl, és a felhasználó nem "végrehajtása" engedélyekkel kell rendelkeznie.

   - **Az előkészítő parancsprogram fájl**: Engedély "700."  Csak a "root" felhasználó rendelkezhet például "olvasási", "write" és "végrehajtási" engedélyeket ehhez a fájlhoz.

   - **Az utólagos parancsprogram** engedély "700." Csak a "root" felhasználó rendelkezhet például "olvasási", "write" és "végrehajtási" engedélyeket ehhez a fájlhoz.

   > [!Important]
   > A keretrendszer nagy teljesítményt biztosít a felhasználóknak. A keretrendszer biztonságos, és győződjön meg arról, csak a "root" felhasználó férhet hozzá a kritikus fontosságú JSON és a parancsfájlokat.
   > Ha a követelmények nem teljesülnek, a szkript nem fut le, mely eredményez olyan fájlok rendszer összeomlása és inkonzisztens biztonsági másolatot.
   >

5. Konfigurálása **VMSnapshotScriptPluginConfig.json** itt leírtak szerint:
    - **pluginName**: Mivel ezt a mezőt hagyja, vagy a parancsfájlok nem a várt módon működik a előfordulhat, hogy.

    - **preScriptLocation**: Adja meg az előkészítő parancsprogram elérési útját a virtuális gépen, amelyet szeretne készíteni.

    - **postScriptLocation**: Adja meg az utólagos parancsprogram elérési útját a virtuális gépen, amelyet szeretne készíteni.

    - **preScriptParams**: Adja meg a választható paraméterek: történő átadása az előkészítő parancsprogram szükséges. Összes paraméter idézőjelek között kell lennie. Ha több paramétert használja, külön paraméterek vesszővel válasszon el.

    - **postScriptParams**: Adja meg a választható paraméterek: történő átadása az utólagos parancsprogram szükséges. Összes paraméter idézőjelek között kell lennie. Ha több paramétert használja, külön paraméterek vesszővel válasszon el.

    - **preScriptNoOfRetries**: Állítsa be, hogy hányszor az előkészítő parancsprogram kell ismételni, ha megszakítása előtt hiba történik. Nulla azt jelenti, hogy csak egy próbálja meg, és nincs újrapróbálkozás, ha hiba történik.

    - **postScriptNoOfRetries**:  Állítsa be, hogy hányszor az utólagos parancsprogram kell ismételni, ha megszakítása előtt hiba történik. Nulla azt jelenti, hogy csak egy próbálja meg, és nincs újrapróbálkozás, ha hiba történik.

    - **Időkorlát_másodpercben**: Adja meg az egyes időtúllépések szkript előtti és az utólagos parancsprogram (a maximális érték 1800 lehet).

    - **continueBackupOnFailure**: Ez az érték **igaz** szeretné-e az Azure Backup térhet vissza a rendszer alkalmazáskonzisztens vagy összeomlás-konzisztens biztonsági mentés Ha előkészítő vagy utólagos parancsfájl sikertelen. Ezt a beállítást **hamis** (kivéve, ha rendelkezik egyszeres lemezként virtuális Gépet, amely visszavált összeomlás-konzisztens biztonsági mentést, ez a beállítás függetlenül) a parancsfájl hiba esetén a biztonsági mentés sikertelen lesz.

    - **fsFreezeEnabled**: Adja meg, hogy Linux fsfreeze kell meghívni, közben, hogy a fájlrendszer-konzisztencia biztosításához a virtuális gép pillanatképét. Javasoljuk, hogy ezt a beállítást állítsa tartja **igaz** , ha az alkalmazás függőséget fsfreeze letiltása.

6. A parancsfájl-keretrendszer van konfigurálva. A virtuális gép biztonsági mentésének már be van állítva, ha a következő biztonsági mentés hívja meg a parancsfájlok, és elindítja az alkalmazáskonzisztens biztonsági mentés. Ha a virtuális gép biztonsági mentése nem történik meg, konfigurálja a [a Recovery Services-tárolók az Azure virtuális gépek biztonsági mentése.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Hibaelhárítás

Ellenőrizze, hogy hozzáadja a megfelelő naplózási a szkript előtti és utáni írása közben, és a szkriptek naplói minden parancsfájl-problémák kijavításához tekintse át. Ha parancsfájlok futtatásához a problémák továbbra is rendelkezik, tekintse meg a következő táblázat további információt.

| Hiba | Hibaüzenet | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Előre-ScriptExecutionFailed |Az előkészítő parancsprogram hibát jelzett, így előfordulhat, hogy a biztonsági másolat nem alkalmazáskonzisztens használható.   | Tekintse meg a probléma megoldásához a parancsfájlt a hibanaplók.|  
|   POST-ScriptExecutionFailed |    Az utólagos parancsprogram hibát jelzett, amelyek hatással lehetnek az alkalmazás állapotát. |    Tekintse meg a probléma megoldásához, és az alkalmazás állapotának ellenőrzéséhez a parancsfájl a hibanaplók. |
| Előre-ScriptNotFound |  Az előkészítő parancsprogram nem található a megadott helyen található a **VMSnapshotScriptPluginConfig.json** konfigurációs fájlban. |   Ügyeljen arra, hogy az előkészítő parancsprogram elérhető alkalmazáskonzisztens biztonsági mentés biztosításához a pluginconfig.JSON fájlban megadott helyen.|
| POST-ScriptNotFound | Az utólagos parancsprogram nem található a megadott helyen található a **VMSnapshotScriptPluginConfig.json** konfigurációs fájlban. |   Ügyeljen arra, hogy az utólagos parancsprogram elérhető alkalmazáskonzisztens biztonsági mentés biztosításához a pluginconfig.JSON fájlban megadott helyen.|
| IncorrectPluginhostFile | A **Pluginhost** fájlt, amely tartalmaz, a VmSnapshotLinux bővítményt, sérült, ezért az előkészítő és az utólagos parancsprogram nem futtatható, és a biztonsági mentés nem lesz alkalmazáskonzisztens. | Távolítsa el a **VmSnapshotLinux** bővítményt, és automatikusan újratelepíti a következő biztonsági mentés, a probléma megoldása érdekében. |
| IncorrectJSONConfigFile | A **VMSnapshotScriptPluginConfig.json** fájl helytelen, ezért az előkészítő parancsprogram és az utólagos parancsprogram nem futtatható, és a biztonsági mentés nem lesz alkalmazáskonzisztens. | Töltse le a másolatot [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) és állítsa be újra. |
| InsufficientPermissionforPre-parancsfájl | Parancsfájlok futtatásához, "root" felhasználó a fájl tulajdonosának kell lennie, és a fájl "700" engedélyekkel kell rendelkeznie (csak a "tulajdonos" kell, hogy "olvasási", "write" és "végrehajtási" engedélyeket). | Győződjön meg róla, "root" a felhasználó a "tulajdonos" parancsfájl és, hogy csak a "tulajdonos" tartalmaz "olvasási", "írás" és "végrehajtása" engedélyt. |
| InsufficientPermissionforPost-parancsfájl | Gyökér szintű felhasználó parancsfájlok futtatásához, a fájl tulajdonosának kell lennie, és a fájl "700" engedélyekkel kell rendelkeznie (csak a "tulajdonos" kell, hogy "olvasási", "write" és "végrehajtási" engedélyeket). | Győződjön meg róla, "root" a felhasználó a "tulajdonos" parancsfájl és, hogy csak a "tulajdonos" tartalmaz "olvasási", "írás" és "végrehajtása" engedélyt. |
| Előre-ScriptTimeout | Az alkalmazáskonzisztens biztonsági mentés előtti parancsfájl időkorlátja lejárt végrehajtását. | Ellenőrizze a parancsfájlt, és növelheti az időkorlátot az a **VMSnapshotScriptPluginConfig.json** címen található fájl **/etc/azure**. |
| POST-ScriptTimeout | Az alkalmazáskonzisztens biztonsági mentés utáni parancsfájl végrehajtása túllépte az időkorlátot. | Ellenőrizze a parancsfájlt, és növelheti az időkorlátot az a **VMSnapshotScriptPluginConfig.json** címen található fájl **/etc/azure**. |

## <a name="next-steps"></a>További lépések
[Konfigurálja a virtuális gép biztonsági mentése Recovery Services-tárolóba](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
