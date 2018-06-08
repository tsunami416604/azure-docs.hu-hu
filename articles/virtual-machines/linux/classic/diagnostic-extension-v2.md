---
title: Linux virtuális Gépet egy Virtuálisgép-bővítménnyel figyelése |} Microsoft Docs
description: 'Útmutató: a Linux diagnosztikai bővítmény a figyelheti a teljesítmény- és a Linux virtuális gépek az Azure diagnosztikai adatokat.'
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
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "32193042"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Linuxos VM teljesítmény- és diagnosztikai adatainak monitorozása a linuxos diagnosztikai bővítménnyel

Ez a dokumentum ismerteti a Linux diagnosztikai bővítmény 2.3 verziója.

> [!IMPORTANT]
> Jelen verziója elavult, és lehet, hogy közzé nem tett 2018. június 30 követően. Helyette 3.0-s verziója. További információkért lásd: a [3.0-s verziója a Linux-diagnosztikai bővítmény dokumentációja](../diagnostic-extension.md).

## <a name="introduction"></a>Bevezetés

(**Megjegyzés**: A Linux diagnosztikai kiterjesztés nyílt forrása [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) , amennyiben először közzé a legfrissebb információkkal a kiterjesztést. Érdemes ellenőrizni a [GitHub-oldalon](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) első.)

A Linux diagnosztikai bővítmény segít a felhasználói figyelése a Microsoft Azure-on futó Linux virtuális gépek. A következő képességekkel rendelkezik:

* Gyűjti, és feltölti a felhasználói tárolási tábla, diagnosztikai és a rendszernaplóba párbeszédeket a Linux virtuális Gépet a teljesítmény-információkat.
* Lehetővé teszi a felhasználók a gyűjtött és a feltöltött adatok metrikák testreszabásához.
* Lehetővé teszi a felhasználók egy kijelölt tároló táblához megadott naplófájlok feltöltéséhez.

2.3 a jelenlegi verzióban szerepel:

* Az összes Linux Rsyslog naplókat, beleértve a rendszer, a biztonsági és az alkalmazás naplóiban.
* Összes rendszer adat, amely meg van adva [a System Center Cross Platform megoldások hely](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Felhasználó által meghatározott naplófájlokat.

Ezt a bővítményt a klasszikus és Resource Manager üzembe helyezési modellel működik.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>A bővítményt, és a régi változatainak elavulása jelenlegi verziója

A bővítmény legújabb verziója **2.3**, és **a régi verziókat (2.0, 2.1-es és 2.2) a rendszer elavult, majd közzé nem tett az év (2017) végéig**. Ha telepítette a Linux diagnosztikai bővítmény automatikus alverzió frissítés le van tiltva, erősen ajánlott, hogy távolítsa el a bővítményt, és engedélyezve van az automatikus alverzió frissítést újra kell telepítenie. (ASM) klasszikus virtuális gépeken érhető el ez a bővítmény Azure XPLAT parancssori felületen vagy a Powershellen keresztül telepítésekor "2.*" megadásával verzióval. ARM gépeken lehet ezt elérni-ot ""autoUpgradeMinorVersion": true" az a virtuális gép központi telepítési sablont. A bővítmény bármely új telepítése is, az automatikus frissítési beállítás engedélyezve van a alverzió kell rendelkeznie.

## <a name="enable-the-extension"></a>A bővítmény engedélyezése

A bővítmény használatával engedélyezheti a [Azure-portálon](https://portal.azure.com/#), az Azure PowerShell vagy Azure CLI-parancsfájlokat.

Tekintheti meg és konfigurálhatja a rendszer és a teljesítmény adatoknak közvetlenül az Azure-portálon, hajtsa végre a [ezeket a lépéseket az Azure blog](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Ez a cikk foglalkozik, hogyan engedélyezése és konfigurálása a bővítmény Azure CLI-parancsok segítségével. Ez lehetővé teszi, hogy olvassa el, és tekintse meg az adatok közvetlenül a tároló tábla.

Vegye figyelembe, hogy az itt ismertetett konfiguráció módszerek az Azure portál nem fognak működni. Tekintheti meg és konfigurálhatja a rendszer és a teljesítmény adatoknak közvetlenül az Azure-portálon, a bővítmény engedélyezni kell a portálon keresztül.

## <a name="prerequisites"></a>Előfeltételek

* **Az Azure Linux ügynök 2.0.6 verzió vagy újabb**.

  Vegye figyelembe, hogy a legtöbb Azure virtuális gép Linux gyűjtemény lemezképei tartalmazzák 2.0.6 verzió vagy újabb. Futtathat **WAAgent-verzió** annak ellenőrzéséhez, hogy melyik verzióját a virtuális Gépen. Ha a virtuális gép 2.0.6 korábbi verziót futtat, amelyeket követve [ezeket az utasításokat a Githubon](https://github.com/Azure/WALinuxAgent "utasításokat") frissíti.
* **Azure parancssori felület (CLI)**. Hajtsa végre a [Ez az útmutató a parancssori felület telepítése](../../../cli-install-nodejs.md) az Azure CLI-környezetet a számítógépen. Az Azure parancssori felület telepítése után is használhatja a **azure** parancsot a parancssori felület (a Bash, a Terminálszolgáltatások vagy a parancssorból) az Azure parancssori felület parancsait eléréséhez. Példa:

  * Futtatás **azure virtuálisgép-bővítmény készlet – súgó** vonatkozó részletes információk.
  * Futtatás **azure bejelentkezési** bejelentkezni az Azure-bA.
  * Futtatás **azure virtuális gép lista** rendelkezik az Azure virtuális gépek listáját.
* Egy tárfiókot, az adatok tárolásához. A korábban létrehozott tárfiók neve és a hívóbetű feltölteni az adatokat a tárhelyre lesz szüksége.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Az Azure CLI parancs segítségével a Linux diagnosztikai bővítmény engedélyezése

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>1. forgatókönyv. Engedélyezi a bővítményt, az alapértelmezett adatkészlet

2.3-as vagy újabb verziója, az alapértelmezett gyűjtendő szerepel:

* Minden Rsyslog információk (beleértve a rendszer, a biztonsági és az alkalmazásnaplók).  
* Egy sor alapján a rendszer adatai. Vegye figyelembe, hogy a teljes adatkészlet a leírása a [System Center Cross Platform megoldások hely](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Ha további adatokat engedélyezni szeretné, további lépéseket a forgatókönyvek 2 és 3.

1. lépés Hozzon létre egy fájlt PrivateConfig.json a következő tartalommal:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

2. lépés Futtatás **azure virtuálisgép-bővítmény beállítása vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>2. forgatókönyv. A figyelő metrikák testreszabása

Ez a szakasz ismerteti, hogyan szabhatja testre a teljesítményt, és diagnosztikai adatokat.

1. lépés A tartalom 1. forgatókönyv ismertetett PrivateConfig.json nevű fájl létrehozása. PublicConfig.json nevű fájlt is létrehozhat. Adja meg az adott adatokat szeretne gyűjteni.

Minden támogatott szolgáltatók és változók, lásd a [System Center Cross Platform megoldások hely](https://scx.codeplex.com/wikipage?title=xplatproviders). Több lekérdezés van, és további lekérdezések hozzáfűzésével a parancsfájl több táblát is tárolhatja őket.

Alapértelmezés szerint a Rsyslog mindig adatgyűjtés.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


2. lépés Futtatás **azure virtuálisgép-bővítmény beállítása vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*"--magánfelhő-config-path PrivateConfig.json – nyilvános-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>3. forgatókönyv. Töltse fel a saját naplófájlok

Ez a szakasz ismerteti, hogyan összegyűjtésére, és töltse fel a megadott naplófájlokat a tárfiókhoz. Meg kell adnia az elérési útját, a naplófájl, és a tábla, hol szeretné tárolni a napló nevét. A parancsfájl több fájl vagy a tábla bejegyzés hozzáadásával több naplófájl is létrehozhat.

1. lépés A tartalom 1. forgatókönyv ismertetett PrivateConfig.json nevű fájl létrehozása. Ezután hozzon létre egy másik nevű PublicConfig.json a következő tartalommal:

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

Vegye figyelembe, hogy ezzel a beállítással a bővítmény verzióin 2.3 előtt minden naplóz írt `/var/log/mysql.err` előfordulhat, hogy másolja, `/var/log/syslog` (vagy `/var/log/messages` attól függően, hogy a Linux distro) is. Ha azt szeretné, az ismétlődő naplózási elkerülésére, kizárhatja naplózása `local6` létesítmény rsyslog konfigurációs naplózza. A Linux distro függ, de az Ubuntu 14.04 rendszeren, a fájl módosítása `/etc/rsyslog.d/50-default.conf` és a sor lecserélheti `*.*;auth,authpriv.none -/var/log/syslog` való `*.*;auth,authpriv,local6.none -/var/log/syslog`. Ez a probléma fennáll a legújabb gyorsjavítások kiadása a 2.3 (2.3.9007), így a bővítmény 2.3 verziója van, ha a probléma nem következik. Ha továbbra is támogatja a virtuális gép újraindítása után is, lépjen velünk kapcsolatba, és segítsen hibaelhárítása, ezért a legújabb gyorsjavítások nem települ automatikusan.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>4. forgatókönyv. A bővítmény a naplók gyűjtésére leállítása

Ez a szakasz ismerteti, hogy a kiterjesztést a naplók gyűjtésére. Vegye figyelembe, hogy a figyelési ügynök folyamat továbbra is működik, és az újrakonfigurálás mellett is. Ha szeretné a figyelési ügynök folyamat teljesen leállhat, a bővítmény letiltásával is megteheti. Letiltja a bővítményt a parancs `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

1. lépés A tartalom 1. forgatókönyv ismertetett PrivateConfig.json nevű fájl létrehozása. Hozzon létre egy másik nevű PublicConfig.json a következő tartalommal:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


2. lépés Futtatás **azure virtuálisgép-bővítmény beállítása vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*"--magánfelhő-config-path PrivateConfig.json – nyilvános-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Tekintse át az adatokat

A teljesítmény- és diagnosztikai adatokat egy Azure Storage tábla vannak tárolva. Felülvizsgálati [használata az Azure Table Storage-ának Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) megtudhatja, hogyan férhet hozzá a tárolási tábla Azure CLI-parancsfájlok használatával.

Ezenkívül segítségével következő felhasználói felületi eszközökkel érik el az adatokat:

1. Visual Studio Server Explorer. Lépjen a tárfiókhoz. Miután a virtuális gép fut, körülbelül 5 percig, látni fogja, a négy alapértelmezett táblák: "LinuxCpu", "LinuxDisk", "LinuxMemory" és "Linuxsyslog". Kattintson duplán a táblanevek, az adatok megtekintéséhez.
1. [Az Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Tártallózó").

![Kép](./media/diagnostic-extension/no1.png)

Ha engedélyezte a fileCfg vagy perfCfg (leírt forgatókönyvek 2 és 3), a Visual Studio Server Explorer és az Azure Tártallózó használhatja nem alapértelmezett adatainak megtekintéséhez.

## <a name="known-issues"></a>Ismert problémák

* A Rsyslog információkat és az ügyfél által megadott naplófájl csak használható parancsprogramok használatával.
