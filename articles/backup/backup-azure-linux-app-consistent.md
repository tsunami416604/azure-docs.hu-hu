---
title: "Az Azure Backup: a Linux virtuális gépek alkalmazáskonzisztens biztonsági mentések |} Microsoft Docs"
description: "Az Azure-ba, a Linux virtuális gépek alkalmazáskonzisztens biztonsági mentés létrehozása. Ez a cikk ismerteti, hogy a parancsfájl-keretrendszer biztonsági mentése Azure telepített Linux virtuális gépek konfigurálása. Ez a cikk hibaelhárítási információkat is."
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
ms.date: 1/12/2018
ms.author: anuragm;markgal
ms.openlocfilehash: c2437b4cd90deda3e7239d87837a47a072f52835
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Azure Linux virtuális gépek alkalmazáskonzisztens biztonsági mentését

Ha a virtuális gépek biztonsági mentési pillanatképek készítése, alkalmazás konzisztencia azt jelenti, hogy az alkalmazások indítása, ha a virtuális gépek helyreállítása után indítsa el. Mivel is tekinthető, rendkívül fontos a alkalmazás következetesség. Győződjön meg arról, hogy a Linux virtuális gépek konzisztens, a Linux-parancsfájl előtti és utáni parancsfájl keretrendszer használatával alkalmazáskonzisztens biztonsági másolatok készítése alkalmazás. A parancsfájl előtti és utáni parancsfájl keretrendszer támogatja az Azure Resource Manager telepített Linux virtuális gépek. Parancsfájl-alkalmazás konzisztencia nem támogatják a Service Manager telepített virtuális gépek vagy windowsos virtuális gépekre.

## <a name="how-the-framework-works"></a>A keretrendszer működése

A keretrendszer egyéni előtti parancsfájlok futtatásához lehetőséget biztosít, és utáni parancsfájlok, amíg a virtuális gép pillanatképek van véve. Előtti parancsfájlok futtatása csak a Virtuálisgép-pillanatképét, és rögtön a virtuális gép pillanatkép készítése utáni parancsfájlok végrehajtása előtt. Előtti és utáni parancsfájlok rugalmasság biztosítása a vezérlésére az alkalmazás és a környezet, amíg a virtuális gép pillanatképek van véve.

Előtti parancsfájlok el natív alkalmazás API-k, mely fokozatosan leválasztani az IOs, és a memórián belüli tartalom kiüríteni a lemezre. Ezek a műveletek legyen a pillanatkép konzisztens alkalmazás. Utáni parancsfájlok natív alkalmazás API-k segítségével az IOs, amely lehetővé teszi az alkalmazások újraindítani a virtuális gép pillanatkép utáni olvasztása.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Parancsfájl előtti és utáni parancsfájl konfigurálásának lépései

1. Jelentkezzen be a Linux virtuális gép, amelyet szeretne biztonsági másolatot készíteni a gyökér szintű felhasználóként.

2. A [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), töltse le **VMSnapshotScriptPluginConfig.json** és másolja a **/etc/azure** mappa az összes virtuális gépet, készítsen biztonsági másolatot szeretne. Ha a **/etc/azure** mappa nem létezik, hozza létre.

3. Másolja a parancsfájl előtti és utáni parancsfájl az összes virtuális gépet, készítsen biztonsági másolatot szeretne az alkalmazáshoz. A parancsfájlok másolhatja egyik helyen, a virtuális Gépen. Ne felejtse el frissíteni a parancsfájlok az elérési útját a **VMSnapshotScriptPluginConfig.json** fájlt.

4. Ellenőrizze, hogy ezeket a fájlokat az alábbi engedélyeket:

   - **VMSnapshotScriptPluginConfig.json**: engedély "600." Például csak a "Gyökér" felhasználó "read" és "write" engedélyekkel kell rendelkeznie a fájl, és a felhasználó nem "hajtható végre" engedélyekkel kell rendelkeznie.

   - **Előtti parancsfájl**: engedély "700."  Például csak a "Gyökér" felhasználói rendelkeznie kell "Olvasás", "write" és "végrehajtási" engedélyeket ehhez a fájlhoz.
  
   - **Utáni parancsfájl** engedély "700." Például csak a "Gyökér" felhasználói rendelkeznie kell "Olvasás", "write" és "végrehajtási" engedélyeket ehhez a fájlhoz.

   > [!Important]
   > A keretrendszer teljesítményigényű nyújt a felhasználóknak. A keretrendszer biztonságos, és győződjön meg arról, csak a "Gyökér" felhasználó rendelkezik hozzáférési jogosultsággal a kritikus JSON és a parancsfájlokat.
   > A feltételeknek nem felel meg, ha a parancsfájl futtatásának, mely eredményezi egy fájl rendszerösszeomlás és konzisztens biztonsági mentést.
   >

5. Konfigurálása **VMSnapshotScriptPluginConfig.json** itt leírtak szerint:
    - **pluginName**: mivel ezt a mezőt hagyja, vagy a parancsfájlok nem működnek megfelelően.

    - **preScriptLocation**: Adja meg a előtti parancsfájl elérési útját a virtuális Gépen, amelyet szeretne biztonsági másolatot.

    - **postScriptLocation**: Adja meg a utáni parancsfájl elérési útját a virtuális Gépen, amelyet szeretne biztonsági másolatot.

    - **preScriptParams**: Adja meg a választható paraméterek: a előtti parancsfájl átadandó igénylő. Minden paraméter idézőjelek között kell lennie. Ha több paramétert használja, külön paraméterek vesszővel válassza el.

    - **postScriptParams**: Adja meg a választható paraméterek: a utáni parancsfájl átadandó igénylő. Minden paraméter idézőjelek között kell lennie. Ha több paramétert használja, külön paraméterek vesszővel válassza el.

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

## <a name="next-steps"></a>További lépések
[Virtuális gép biztonsági mentést a Recovery Services-tároló konfigurálása](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
