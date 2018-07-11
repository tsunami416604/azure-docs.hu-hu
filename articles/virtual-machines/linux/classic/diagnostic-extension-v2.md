---
title: Linux virtuális Gépet egy Virtuálisgép-bővítménnyel figyelése |} A Microsoft Docs
description: Ismerje meg, a Linux diagnosztikai bővítmény használata a Teljesítményfigyelő és diagnosztikai adatok az Azure-ban Linux rendszerű virtuális gépek.
services: virtual-machines-linux
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: f1415e2cfbe48b287db5851bb8ebef1ff9251280
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "32193042"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Linuxos VM teljesítmény- és diagnosztikai adatainak monitorozása a linuxos diagnosztikai bővítménnyel

Ez a dokumentum ismerteti a Linux diagnosztikai bővítmény 2.3-as verzióját.

> [!IMPORTANT]
> Ezen verziója elavult, és lehet közzé nem tett 2018. június 30. után bármikor. 3.0-s verzió váltotta fel. További információkért lásd: a [a Linux diagnosztikai bővítmény 3.0-s verzió dokumentációjában](../diagnostic-extension.md).

## <a name="introduction"></a>Bevezetés

(**Megjegyzés**: A Linux diagnosztikai bővítmény, a nyílt forráskódú [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) , ahol az első közzététel a kiterjesztéséről a legfrissebb információkkal. Előfordulhat, hogy szeretné ellenőrizni a [GitHub-oldalon](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) első.)

A Linux diagnosztikai bővítmény egy felhasználó figyelése a Microsoft Azure-ban futó Linux rendszerű virtuális gépek segítségével. Az alábbi képességekkel rendelkezik:

* Gyűjti, és feltölti a rendszer teljesítményadatok a Linux rendszerű virtuális gépekről a felhasználó storage-táblához, beleértve a diagnosztikai és a syslog-információkat.
* Lehetővé teszi a felhasználók számára a gyűjtött és a feltöltött adatok metrikáinak testreszabása.
* Lehetővé teszi a felhasználók egy kijelölt tárolótáblában megadott naplófájlok feltöltéséhez.

2.3 a jelenlegi verzióban a adatok tartalma:

* Az összes Linux Rsyslog naplók, beleértve a rendszer, a biztonság és az alkalmazásnaplókat.
* A megadott összes rendszeradatok [a System Center-Adatbázisközi platformot megoldásokat hely](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Felhasználó által meghatározott naplófájlokat.

Ezt a bővítményt a klasszikus és Resource Manager üzembe helyezési modellel működik.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>A bővítmény és a régi verziók elavulása jelenlegi verziója

A bővítmény legújabb verziója **2.3**, és **bármely régi verziói (2.0-s, 2.1-es és 2.2-es) elavult és közzé nem tett ebben az évben (2017) végén**. Ha a Linux diagnosztikai bővítmény le van tiltva az automatikus alverzió frissítés telepítve van, erősen ajánlott, hogy távolítsa el a bővítményt, és engedélyezve van az automatikus alverzió frissítést újra kell telepítenie. Klasszikus (ASM) virtuális gépen akkor ezt úgy érheti megadása "2.*" verzióval, ha telepíti a bővítményt, az Azure-XPLAT parancssori felület vagy a Powershellen keresztül. ARM gépeken érhet el ez azzal ""autoUpgradeMinorVersion": true" az a virtuális gép központi telepítési sablont. Minden olyan új a bővítmény telepítését az is, az automatikus frissítési funkcióját alverzió kell rendelkeznie.

## <a name="enable-the-extension"></a>A bővítmény engedélyezése

Ez a bővítmény használatával engedélyezheti a [az Azure portal](https://portal.azure.com/#), az Azure PowerShell vagy az Azure CLI-szkriptek.

Adatát megtekintheti és konfigurálhatja a rendszer és a teljesítmény közvetlenül az Azure Portalról, hajtsa végre a [ezeket a lépéseket az Azure-blogban](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Ez a cikk foglalkozik, hogyan engedélyezheti és konfigurálhatja a bővítmény az Azure CLI-parancsok használatával. Ez lehetővé teszi, hogy olvassa el, és megtekintheti az adatokat közvetlenül a storage-táblából.

Vegye figyelembe, hogy az itt ismertetett konfiguráció módszerek az Azure Portalon nem fog működni. Adatát megtekintheti és konfigurálhatja a rendszer és a teljesítmény közvetlenül az Azure Portalról, a bővítményt engedélyezni kell a portálon keresztül.

## <a name="prerequisites"></a>Előfeltételek

* **Az Azure Linux-ügynök 2.0.6-os verzió vagy újabb**.

  Fontos megjegyezni, hogy a legtöbb Azure-beli virtuális gép Linuxos katalógus lemezképek tartalmazzák-e 2.0.6-os verzió vagy újabb. Futtathat **WAAgent-verzió** annak ellenőrzéséhez, hogy melyik verziója van telepítve a virtuális gépen. Ha a virtuális gép egy korábbi, mint a 2.0.6-os verzió fut, követheti [ezeket az utasításokat a Githubon](https://github.com/Azure/WALinuxAgent "utasításokat") frissíteni.
* **Azure parancssori felület (CLI)**. Hajtsa végre a [Ez az útmutató a parancssori felület telepítése](../../../cli-install-nodejs.md) az Azure CLI-környezetet a gépen. Miután az Azure CLI telepítve van, használhatja a **azure** parancsot a parancssori felületről (Bash, a terminált vagy parancssort) eléréséhez az Azure CLI-parancsokat. Példa:

  * Futtatás **azure virtuális gép bővítmény set--help** részletes súgójában talál.
  * Futtatás **azure bejelentkezési** bejelentkezni az Azure-bA.
  * Futtatás **azure virtuálisgép-lista** listázhatja az összes virtuális gépet, amely rendelkezik az Azure-ban.
* Az adatok tárolása a storage-fiók. Szüksége lesz a korábban létrehozott tárfiók neve és a egy hozzáférési kulcsra az adatok feltöltése a tárolóba.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Az Azure CLI-parancs segítségével a Linux diagnosztikai bővítmény engedélyezése

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>1. forgatókönyv. Engedélyezi a bővítményt az alapértelmezett adatkészlet

2.3-as vagy újabb verziója, az alapértelmezett gyűjtendő adatokat tartalmazza:

* Az összes Rsyslog információk (beleértve a rendszer, a biztonság és az alkalmazásnaplók).  
* Alapszintű készlete alapján a rendszer adatai. Vegye figyelembe, hogy a teljes adatkészletet a leírása a [System Center-Adatbázisközi platformot megoldásokat hely](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Ha szeretné engedélyezni, további adatokat, folytassa a forgatókönyvek 2. és 3 lépéseit.

1. lépés Hozzon létre egy fájlt PrivateConfig.json az alábbi tartalommal:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

2. lépés Futtatás **azure virtuálisgép-bővítmény beállítása vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>2. forgatókönyv. A teljesítmény monitor metrikáinak testreszabása

Ez a szakasz ismerteti, hogyan szabhatja testre a teljesítményt, és diagnosztikai adatokat.

1. lépés Hozzon létre egy fájlt PrivateConfig.json leírt forgatókönyv 1. a tartalommal. PublicConfig.json nevű fájlt is létrehozhat. Adja meg a gyűjteni kívánt adatokat.

Az összes támogatott szolgáltatók és változókat, hivatkozhat a [System Center-Adatbázisközi platformot megoldásokat hely](https://scx.codeplex.com/wikipage?title=xplatproviders). Több lekérdezés van, és úgy, több lekérdezés a parancsfájl több tábla tárolja őket.

Alapértelmezés szerint a Rsyslog mindig az adatgyűjtés történik.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


2. lépés Futtatás **azure virtuálisgép-bővítmény beállítása vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*"--private-config-path PrivateConfig.json--public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>3. forgatókönyv. A saját naplófájlok

Ez a szakasz ismerteti, hogyan gyűjthet, és töltse fel a megadott naplófájlokat a tárfiókhoz. Adja meg az elérési útját, a naplófájl, és a tábla, ahol szeretné tárolni a napló nevét kell. Több fájl vagy-tábláját bejegyzést ad hozzá a parancsfájl több naplófájl is létrehozhat.

1. lépés Hozzon létre egy fájlt PrivateConfig.json leírt forgatókönyv 1. a tartalommal. Ezután hozzon létre egy másik nevű PublicConfig.json az alábbi tartalommal:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

2. lépés Futtassa az `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json` parancsot.

Vegye figyelembe, hogy ezt a beállítást a korábbi 2.3 bővítmény-verziók, az összes naplók írt `/var/log/mysql.err` előfordulhat, hogy megkettőzhetők, így `/var/log/syslog` (vagy `/var/log/messages` attól függően, a Linux-disztribúció) is. Ha meg szeretné kerülni a duplikált naplózás, naplózását, kizárhatja `local6` létesítmény bejelentkezik az rsyslog konfigurációját. Attól függ, a Linux-disztribúció, de az Ubuntu 14.04 rendszeren, a fájl módosítása `/etc/rsyslog.d/50-default.conf` is cserélje le a sort, és `*.*;auth,authpriv.none -/var/log/syslog` való `*.*;auth,authpriv,local6.none -/var/log/syslog`. Ez a probléma kijavítása a legújabb gyorsjavítások kiadásában 2.3-as (2.3.9007), így a bővítmény 2.3-as verzióval rendelkezik, ha a probléma nem fordulhat elő. Ha továbbra is támogatja a virtuális gép újraindítása után is, lépjen kapcsolatba velünk, és segítsen a legújabb gyorsjavítások nem települ automatikusan okozó hibák elhárításához.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>4. forgatókönyv. A bővítmény bármely naplók gyűjtésére leállítása

Ez a szakasz ismerteti, hogy a bővítmény naplók gyűjtésére. Vegye figyelembe, hogy a monitorozási ügynök folyamat továbbra is működik és az újrakonfigurálás mellett is. Ha szeretné a monitorozási ügynök folyamat teljesen leállhat, a bővítmény letiltásával megteheti. A parancs letiltja a bővítményt a következő `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

1. lépés Hozzon létre egy fájlt PrivateConfig.json leírt forgatókönyv 1. a tartalommal. Hozzon létre egy másik nevű PublicConfig.json az alábbi tartalommal:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


2. lépés Futtatás **azure virtuálisgép-bővítmény beállítása vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*"--private-config-path PrivateConfig.json--public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Tekintse át az adatokat

A teljesítmény- és diagnosztikai adatokat egy Azure Storage-táblába vannak tárolva. Felülvizsgálat [Azure Table Storage, a Ruby használata](../../../cosmos-db/table-storage-how-to-use-ruby.md) megtudhatja, hogyan érhető el a tárolási táblában lévő adatokat az Azure CLI-szkriptek használatával.

Emellett használhatja a következő felhasználói felületi eszközök férhet hozzá az adatokhoz:

1. Visual Studio Server Explorer. Lépjen a tárfiókhoz. Miután a virtuális gép nagyjából öt perc alatt fut, látni fogja a négy alapértelmezett táblák: "LinuxCpu", "LinuxDisk", "LinuxMemory" és "Linuxsyslog". Kattintson duplán a táblanevek, az adatok megtekintéséhez.
1. [Az Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "az Azure Storage Explorer").

![image](./media/diagnostic-extension/no1.png)

Ha engedélyezte a fileCfg vagy perfCfg (leírt forgatókönyvek 2. és 3), Visual Studio Server Explorerben és az Azure Storage Explorer használatával nem alapértelmezett adatok megtekintéséhez.

## <a name="known-issues"></a>Ismert problémák

* Az Rsyslog információkat és az ügyfél által megadott naplófájl csak elérhetők parancsfájlok használatával.
