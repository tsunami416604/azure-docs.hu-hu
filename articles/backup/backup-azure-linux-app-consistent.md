---
title: "Az Azure Backup: a Linux virtuális gépek alkalmazáskonzisztens biztonsági mentések |} Microsoft Docs"
description: "Parancsfájlok segítségével alkalmazáskonzisztens biztonsági mentések garantálja az Azure-ba, a Linux virtuális gépek számára. A parancsfájlok csak Linux virtuális gépek erőforrás-kezelő telepítések; vonatkoznak a parancsfájlok nem vonatkoznak a Windows virtuális gépek vagy a service manager központi telepítéseket. Ez a cikk végigvezeti a parancsfájlok, beleértve a hibaelhárítás konfigurálásának lépéseit."
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "alkalmazáskonzisztens biztonsági másolat; Azure virtuális gép alkalmazáskonzisztens biztonsági mentését; Linux virtuális gép biztonsági mentése; Azure biztonsági mentés"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Alkalmazáskonzisztens biztonsági mentését Azure Linux virtuális gépek (előzetes verzió)

Ez a cikk megbeszélések kapcsolatos a Linux-keretrendszer, és hogyan használható az Azure Linux virtuális gépek alkalmazáskonzisztens biztonsági másolatok készítése utáni parancsfájl, és előre parancsfájlok.

> [!Note]
> A parancsfájl előtti és utáni parancsfájl keretrendszer csak Azure Resource Manager telepített Linux virtuális gépek esetén támogatott. Parancsfájl-alkalmazás konzisztencia nem támogatottak a Service Manager telepített virtuális gépek vagy windowsos virtuális gépekre.
>

## <a name="how-the-framework-works"></a>A keretrendszer működése

A keretrendszer egyéni előtti parancsfájlok futtatásához lehetőséget biztosít, és utáni parancsfájlok, amíg a virtuális gép pillanatképek van véve. Előtti parancsfájlok a virtuális gép pillanatképet, és utáni parancsfájlok azonnal, miután a virtuális gép pillanatképének készítése előtt. Ez lehetővé teszi a rugalmasságot az alkalmazás és a környezet szabályozására, amíg a virtuális gép pillanatképek van véve.

Ebben a forgatókönyvben fontos alkalmazáskonzisztens a virtuális gép biztonsági mentés. A előtti parancsfájl fokozatosan leválasztani az IOs API-alkalmazás natív elindíthat és memórián belüli tartalom kiüríteni a lemezre. Ez biztosítja, hogy a pillanatkép alkalmazáskonzisztens (Ez azt jelenti, hogy az alkalmazás előre fel a virtuális gép indításakor visszaállítás utáni). Az IOs olvasztása utáni parancsfájl használható. Ennek érdekében alkalmazás natív API-val, hogy az alkalmazás folytathatja a normál működés post-virtuális gép pillanatfelvétele.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Parancsfájl előtti és utáni parancsfájl konfigurálásának lépései

1. Jelentkezzen be a Linux virtuális gép, amelyet szeretne biztonsági másolatot készíteni a gyökér szintű felhasználóként.

2. Töltse le **VMSnapshotScriptPluginConfig.json** a [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), és másolja azt a **/etc/azure** mappájában található összes virtuális gépet, készítsen biztonsági másolatot fog. Hozzon létre a **/etc/azure** könyvtárat, ha már nem létezik.

3. Másolja a előtti parancsfájl és utáni parancsfájl az összes virtuális gépet, készítsen biztonsági másolatot szeretne az alkalmazáshoz. A parancsfájlok másolhatja egyik helyen, a virtuális Gépen. Ne felejtse el frissíteni a parancsfájlok az elérési útját a **VMSnapshotScriptPluginConfig.json** fájlt.

4. Ellenőrizze, hogy ezeket a fájlokat az alábbi engedélyeket:

   - **VMSnapshotScriptPluginConfig.json**: engedély "600." Például csak a "Gyökér" felhasználó "read" és "write" engedélyekkel kell rendelkeznie a fájl, és a felhasználó nem "hajtható végre" engedélyekkel kell rendelkeznie.

   - **Előtti parancsfájl**: engedély "700."  Például csak a "Gyökér" felhasználói rendelkeznie kell "Olvasás", "write" és "végrehajtási" engedélyeket ehhez a fájlhoz.
  
   - **Utáni parancsfájl** engedély "700." Például csak a "Gyökér" felhasználói rendelkeznie kell "Olvasás", "write" és "végrehajtási" engedélyeket ehhez a fájlhoz.

   > [!Important]
   > A keretrendszer teljesítményigényű nyújt a felhasználóknak. Fontos, hogy biztonságos, és csak "Gyökér" felhasználó hozzáfér a kritikus JSON és a parancsfájl fájlokhoz.
   > A korábbi feltételeknek nem felel meg, ha a parancsprogram nem futott. Az eredmény rendszerösszeomlás/konzisztens biztonsági mentés.
   >

5. Konfigurálása **VMSnapshotScriptPluginConfig.json** itt leírtak szerint:
    - **pluginName**: mivel ezt a mezőt hagyja, vagy a parancsfájlok nem működnek megfelelően.

    - **preScriptLocation**: Adja meg a előtti parancsfájl elérési útját a virtuális Gépen, amelyet szeretne biztonsági másolatot.

    - **postScriptLocation**: Adja meg a utáni parancsfájl elérési útját a virtuális Gépen, amelyet szeretne biztonsági másolatot.

    - **preScriptParams**: Adja meg a választható paraméterek: a előtti parancsfájl átadandó igénylő. Minden paraméter idézőjelek között kell lennie, és vesszővel elválasztva, ha több paramétert kell lennie.

    - **postScriptParams**: Adja meg a választható paraméterek: a utáni parancsfájl átadandó igénylő. Minden paraméter idézőjelek között kell lennie, és vesszővel elválasztva, ha több paramétert kell lennie.

    - **preScriptNoOfRetries**: a szám, ahányszor a előtti parancsfájl meg kell ismételni, ha hiba történik megszakítása előtt állítsa be. Nulla azt jelenti, hogy csak egy try és nem az újra gombra, ha hiba történik.

    - **postScriptNoOfRetries**: a szám, ahányszor a utáni parancsfájl meg kell ismételni, ha hiba történik megszakítása előtt állítsa be. Nulla azt jelenti, hogy csak egy try és nem az újra gombra, ha hiba történik.
    
    - **Időkorlát_másodpercben**: Adjon meg egyedi időtúllépések a előtti parancsfájl és a utáni parancsfájlt.

    - **continueBackupOnFailure**: az érték **igaz** Ha azt szeretné, ha előtti parancsfájl visszaállni a fájlrendszer konzisztens/összeomlás konzisztens biztonsági mentése vagy utáni parancsfájl sikertelen biztonsági mentési Azure. Ezt a beállítást **hamis** (kivéve, ha rendelkezik egyszerű virtuális Gépet, amely visszavált összeomlásbiztos biztonsági mentésbe ettől a beállítástól függetlenül) a parancsfájl hiba esetén a biztonsági mentés sikertelen lesz.

    - **fsFreezeEnabled**: Adja meg, hogy Linux fsfreeze kell hívható, amíg a fájlrendszer-konzisztencia biztosításához a virtuális gép pillanatkép van véve. Azt javasoljuk, hogy ez a beállítás értéke **igaz** , ha az alkalmazás a fsfreeze letiltása függőség van.

6. A parancsfájl-keretrendszer konfigurálva van. Ha a virtuális gép biztonsági mentés már be van állítva, a következő biztonsági mentés hív meg, a parancsfájlok, és elindítja a alkalmazáskonzisztens biztonsági mentését. Ha a virtuális gép biztonsági mentés van beállítva, konfigurálja úgy a [a Recovery Services-tárolók Azure virtuális gépek biztonsági mentése.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Hibaelhárítás

Győződjön meg arról, adja hozzá a megfelelő naplózási írásakor, a parancsfájl előtti és utáni parancsfájl, és tekintse át a parancsfájl minden parancsfájl problémáinak megoldásával kapcsolatban. Ha továbbra is problémákba-parancsfájlok futtatásakor, tekintse meg a következő táblázat további információt.

| Hiba | Hibaüzenet | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Előre-ScriptExecutionFailed |A előtti parancsfájl hibát jelzett, így előfordulhat, hogy a biztonsági másolat nem használható alkalmazáskonzisztens.   | Tekintse meg a hiba naplókat, javítsa ki a parancsfájlt.|  
|   POST-ScriptExecutionFailed |    Az utólagos parancsfájl, amely hatással lehet a alkalmazásállapot hibát adott vissza. |    Tekintse meg a hiba keresse meg a parancsfájl hárítsa el a problémát, és ellenőrizze az alkalmazás állapotát. |
| Előre-ScriptNotFound |  A előtti parancsfájl a megadott helyen nem található a **VMSnapshotScriptPluginConfig.json** konfigurációs fájlban. |   Győződjön meg arról, hogy előtti parancsfájl verziója alkalmazáskonzisztens biztonsági mentés a konfigurációs fájlban megadott elérési úton található.|
| POST-ScriptNotFound | Az utólagos parancsfájl a megadott helyen nem található a **VMSnapshotScriptPluginConfig.json** konfigurációs fájlban. |   Győződjön meg arról, hogy utáni parancsfájl verziója alkalmazáskonzisztens biztonsági mentés a konfigurációs fájlban megadott elérési úton található.|
| IncorrectPluginhostFile | A **Pluginhost** fájl, amely VmSnapshotLinux kiterjesztésű, sérült, így parancsfájl előtti és utáni parancsfájl nem futtatható, és a biztonsági másolat nem használható alkalmazáskonzisztens. | Távolítsa el a **VmSnapshotLinux** bővítményt, és automatikusan újratelepíti a következő biztonsági másolat, a probléma megoldása érdekében. |
| IncorrectJSONConfigFile | A **VMSnapshotScriptPluginConfig.json** fájl helytelen, így előtti parancsfájl és utáni parancsfájl nem futtatható, és a biztonsági másolat nem használható alkalmazáskonzisztens. | Töltse le a Másolás [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) és állítsa be újra. |
| InsufficientPermissionforPre-parancsfájl | A parancsfájlok futtatásához, a "Gyökér" felhasználói kell a fájl tulajdonosa, és a fájl "700" engedélyekkel kell rendelkezniük (Ez azt jelenti, hogy csak a "tulajdonos" kell "Olvasás", "write" és "végrehajtási" engedélyeket). | Ellenőrizze, hogy "Gyökér" felhasználó a "tulajdonos" a parancsfájl és, hogy csak a "tulajdonos" rendelkezik "Olvasás", "write" és "hajtható végre". |
| InsufficientPermissionforPost-parancsfájl | A parancsfájlok futtatásához, a gyökér szintű felhasználó kell a fájl tulajdonosa, és a fájl "700" engedélyekkel kell rendelkezniük (Ez azt jelenti, hogy csak a "tulajdonos" kell "Olvasás", "write" és "végrehajtási" engedélyeket). | Ellenőrizze, hogy "Gyökér" felhasználó a "tulajdonos" a parancsfájl és, hogy csak a "tulajdonos" rendelkezik "Olvasás", "write" és "hajtható végre". |
| Előre-ScriptTimeout | Az alkalmazáskonzisztens biztonsági mentés előtti parancsfájlt időtúllépés miatt megszakadt végrehajtása. | Ellenőrizze a parancsfájl, és növeli az időkorlátot adja meg a **VMSnapshotScriptPluginConfig.json** címen található fájl **/etc/azure**. |
| POST-ScriptTimeout | A végrehajtás alkalmazáskonzisztens biztonsági mentés utáni parancsfájlja túllépte az időkorlátot. | Ellenőrizze a parancsfájl, és növeli az időkorlátot adja meg a **VMSnapshotScriptPluginConfig.json** címen található fájl **/etc/azure**. |

## <a name="next-steps"></a>Következő lépések
[Virtuális gép biztonsági mentést a Recovery Services-tároló konfigurálása](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
