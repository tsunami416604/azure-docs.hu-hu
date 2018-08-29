---
title: Az Azure virtuális gépek soros konzolja |} A Microsoft Docs
description: Azure-beli virtuális gépek soros konzol és a kétirányú.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: ddd30729aa2bcb616efab814dc4046d2817c64fa
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128677"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuális gépek soros konzolja (előzetes verzió) 


A virtuális gépek soros konzolja az Azure-ban a Linux és Windows virtuális gépek egy szöveges alapú konzol hozzáférést biztosít. A soros kapcsolat COM1 soros porton, a virtuális gép és a virtuális géphez való hozzáférés biztosít, és amelyek nem kapcsolódnak a virtuális gép hálózati / operációs rendszer állapotát. Virtuális gép a soros konzoljához való hozzáférés is jelenleg csak az Azure Portalon keresztül történik, és csak azok számára, akik rendelkeznek a virtuális gép közreműködő vagy újabb a virtuális géphez való hozzáférés engedélyezett. 

A soros konzol dokumentáció Linux rendszerű virtuális gépekhez [ide](../linux/serial-console.md).

> [!Note] 
> Előzetes verziók által elérhető, a feltétellel, hogy elfogadja a használati feltételeket. További információkért lásd: [Microsoft Azure kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ez a szolgáltatás jelenleg a **nyilvános előzetes verzióban** és a soros konzoljához való hozzáférés a virtuális gépek globális Azure-régióban érhető el. Ezen a ponton soros konzol nem érhető el az Azure Government, Azure Germany és Azure China cloud.

 

## <a name="prerequisites"></a>Előfeltételek 

* A resource management üzemi modellhez kell használnia. Klasszikus üzemi modellben nem támogatottak. 
* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)
    
* A soros konzol használatával a fióknak rendelkeznie kell [közreműködői szerepkört](../../role-based-access-control/built-in-roles.md) virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiókot. 
* A virtuális gép, amelyekhez le a soros konzol acessing is jelszóalapú fiókkal kell rendelkeznie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény - funkciójának lásd az alábbi képernyőképet.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Soros konzol használatának első lépései
A virtuális gépek soros konzolon keresztül csak érhető el [az Azure portal](https://portal.azure.com). A virtuális gépekhez a portálon keresztül a soros konzol eléréséhez a lépéseket az alábbiakban 

  1. Nyissa meg az Azure Portalon
  2. A bal oldali menüben válassza ki a virtuális gépeket.
  3. Kattintson a listában a virtuális gépen. A virtuális gép áttekintő oldala nyílik meg.
  4. Görgessen le a támogatás és hibaelhárítás szakaszhoz, és kattintson a soros konzol (előzetes verzió) lehetőséget. A soros konzol segítségével egy új panel megnyitja és indítsa el a kapcsolatot.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="configure-serial-console-for-windows"></a>Soros konzol Windows konfigurálása 
Újabb Windows Server-rendszerképeket az Azure-ban kell [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezve van. SAC a Windows server-verziók esetében támogatott, de nem áll rendelkezésre az ügyfél-verziók (például a Windows 10, Windows 8 vagy Windows 7). Soros konzol használatával létrehozott Windows virtuális gépek engedélyezése Feb2018 vagy alacsonyabb képek használja az alábbi lépéseket: 

1. Csatlakozás a Windows virtuális géphez a távoli asztalon keresztül
2. Egy rendszergazdai parancssorból a következő parancsok futtatásával. 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Indítsa újra a rendszert a SAC konzol engedélyezni kell

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Ha a szükséges SAC engedélyezhető offline is, 

1. SAC adatlemezként meglévő virtuális géphez konfigurált kívánt windows-lemez csatolása. 
2. Egy rendszergazdai parancssorból a következő parancsok futtatásával. 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Honnan tudhatom meg, hogy SAC engedélyezve van-e vagy sem 

Ha a [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve a soros konzol nem jelenik meg a SAC használatával. Bizonyos esetekben egy virtuális gép állapotára vonatkozó megmutatja, vagy üres lenne.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>A soros konzolon megjelenítendő rendszerindító menü engedélyezése 

Ha a Windows rendszertöltő engedélyeznie kell a Windows rendszertöltő adhat hozzá az alábbi további beállítások megjelenítéséhez a soros konzol kéri.

1. Csatlakozás a Windows virtuális géphez a távoli asztalon keresztül
2. Egy rendszergazdai parancssorból a következő parancsok futtatásával. 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Indítsa újra a rendszert, engedélyezni kell a rendszerindító menü

> [!NOTE] 
> Ez a funkció pont támogatása, kulcsok nincs engedélyezve, ha speciális rendszerindítási beállítások használata bcdedit / {jelenlegi} set onetimeadvancedoptions van szüksége, tekintse meg [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) további részletekért

## <a name="disable-serial-console"></a>Tiltsa le a soros konzol
Alapértelmezés szerint minden előfizetés rendelkezik a soros konzol hozzáférés engedélyezve van az összes virtuális gép. Soros konzol vagy az előfizetés szintjén, vagy a virtuális gép szintjén letiltható.

### <a name="subscription-level-disable"></a>Előfizetés-szintű letiltása
Soros konzolon keresztül szerint az egész előfizetésre letiltható a [tiltsa le a konzolon REST API-hívás](https://aka.ms/disableserialconsoleapi). Használhatja a "Kipróbálom" elérhető funkciók az API-dokumentáció oldalon letiltása és engedélyezése a soros konzol egy előfizetés. Adja meg a `subscriptionId`, az "alapértelmezett" a `default` mezőben, majd kattintson az OK gombra. Az Azure CLI-parancsok még nem érhető el, és a egy későbbi időpontban fog megérkezni. [Próbálja ki a REST API-hívást Itt](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Azt is megteheti, előfordulhat, hogy használja az alábbi parancsokat készletét a Cloud Shellben (bash-parancsok látható) letiltása, engedélyezése és egy előfizetést a soros konzol biztonságtiltott állapotának megtekintése. 

* Előfizetés a soros konzol letiltott állapotának lekéréséhez:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Soros konzol egy előfizetés letiltása:
    ```azurecli-interactive 
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Soros konzol egy előfizetés engedélyezése:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Virtuálisgép-szintű letiltása
Soros konzol az adott virtuális gép rendszerindítási diagnosztikai beállítás letiltásával letiltható adott virtuális gépek. Egyszerűen kapcsolja ki a rendszerindítási diagnosztika az Azure Portalról, és a soros konzol a virtuális gép le lesz tiltva.

## <a name="serial-console-security"></a>Soros konzol biztonsága 

### <a name="access-security"></a>Hozzáférés-biztonságot 
Soros konzoljához való hozzáférés a felhasználók, akik rendelkeznek korlátozódik [virtuális gép közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy a virtuális géphez való hozzáférés felett. Ha az AAD-bérlő többtényezős hitelesítést igényel, akkor a soros konzoljához való hozzáférés is szükség van egy MFA-n keresztül van a hozzáférés [az Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Csatorna biztonsági
Hálózati oda-vissza küldött összes adatot titkosított.

### <a name="audit-logs"></a>Naplók
A soros konzoljához való hozzáférés teljes jelenleg be van jelentkezve a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure-beli virtuálisgép-rendszergazda határozza meg.  

>[!CAUTION] 
A konzol hozzáférés jelszavakat nem jelentkezik, ha a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket vagy bármely más, személyazonosításra alkalmas adatok (PII) kimeneti, amíg azok lesz írva a virtuális gép rendszerindítási diagnosztikája a naplókat, és minden egyéb látható szöveg, görgessen a soros konzol végrehajtásának részeként biztonsági funkciók. Ezeket a naplókat. kör alakú, és csak egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező hozzáférése, de javasoljuk, hogy kövesse az ajánlott eljárás szerint az összes adat távoli asztallal, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok. 

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó le lesz választva, és a második felhasználó csatlakoztatott oly módon, mintha az első felhasználó, hogy mobilplatformonként és a fizikai konzol és a egy új elhagyása a felhasználó úgy.

>[!CAUTION] 
Ez azt jelenti, hogy a felhasználó, aki megszakad nem kijelentkeztetésével! Képes érvényesíteni a kijelentkezési (SIGHUP vagy hasonló mechanizmus) keresztül kapcsolat bontása esetén továbbra is szerepel az ütemterv részét képezi. A Windows van egy automatikus időtúllépési engedélyezve van a SAC, azonban a Linuxhoz készült terminál timeout beállítás konfigurálása. 

## <a name="using-serial-console-for-nmi-calls-in-windows-vms"></a>Soros konzol használata NMI meghívja a Windows-beli virtuális gépeken
Egy nem maszkolható (NMI) úgy tervezték, hogy hozzon létre egy olyan jelet, hogy a szoftverek virtuális gépi nem figyelmen kívül hagyja. Hagyományosan NMIs figyelje a hardverekkel kapcsolatos problémák szerepelnek, amelyek adott válaszidők szükséges rendszereken voltak használva.  Ma, programozók és a rendszer a rendszergazdák gyakran használnak NMI mechanizmusként javításához vagy hibaelhárítása a rendszerek, amelyek leáll.

A soros konzol segítségével egy NMI küldeni egy Azure virtuális gépen a billentyűzet ikon használatával az alább látható a parancssávon. Miután a NMI érkezik, a virtuális gép konfigurációja szabályozza hogyan a rendszer reagál. Windows beállítható úgy, hogy az összeomlási, és a memóriakép létrehozása egy NMI fogadásakor.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Windows egy összeomlási memóriakép létrehozása egy NMI kap való konfigurálásával kapcsolatos információkért lásd: [egy teljes összeomlási memóriakép-fájl vagy a kernel összeomlási memóriakép létrehozása egy NMI egy Windows-alapú rendszeren való használatával](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)


## <a name="errors"></a>Hibák
A legtöbb hiba átmeneti jellegű, és ezek újrapróbálkozás a kapcsolódási IP-címet a rendszer. Az alábbi táblázat azon hibákat és kezelési lehetőségeiket listáját

Hiba                            |   Kezelés 
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait "<VMNAME>". A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. 
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. | Virtuális gépnek kell lennie a soros konzol eléréséhez a elindított állapotú
Nem rendelkezik a virtuális gép soros konzol használatához a szükséges engedélyekkel. Győződjön meg arról, hogy Virtuálisgép-közreműködői szerepkör engedélyei.| Soros konzol eléréséhez az egyes hozzáférési engedélyt kell. Lásd: [a hozzáférési követelmények](#prerequisites) részletekért
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának "<STORAGEACCOUNTNAME>". Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Soros konzol eléréséhez az egyes hozzáférési engedélyt kell. Lásd: [a hozzáférési követelmények](#prerequisites) részletekért
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nincs egy fiók tűzfal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.
Web socket le van zárva, vagy nem nyitható meg. | Szükség lehet az engedélyezési listára `*.console.azure.com`. Egy részletesebb, de hosszabb megközelítés az engedélyezési listára a [a Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/en-us/download/details.aspx?id=41653), amelyek viszonylag sűrűn változnak.

## <a name="known-issues"></a>Ismert problémák 
Sok továbbra is szakaszaiban az előzetes verzió a soros hozzáféréshez, hogy bizonyos ismert problémák keresztül dolgozik, alább látható ezen a lehetséges megoldások listája 

Probléma                             |   Kezelés 
:---------------------------------|:--------------------------------------------|
A virtual machine scale set példány soros konzol nem választható | Előzetes verzió idején a virtuális gép méretezési csoport példányaihoz a soros konzoljához való hozzáférés nem támogatott.
Szerezze meg, miután a kapcsolaton transzparens nem jeleníti meg a napló-parancssorban | Tekintse át ezt oldal: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez előfordulhat, hogy fordulhat elő, ha egy egyéni virtuális Gépet futtat, megerősített készülék, vagy a config LÁRVAJÁRAT adott causers Windows megfelelően csatlakozni a soros port sikertelen lesz.
Csak az egészségügyi információk Windows virtuális Géphez való csatlakozáskor jelenik meg| Ez fog megjelenni Ha a speciális felügyeleti konzol nincs engedélyezve a Windows-lemezkép számára. Lásd: [hozzáférés soros konzol a Windows](#access-serial-console-for-windows) manuálisan engedélyezni a virtuális Gépen Windows SAC létrehozásával kapcsolatos útmutatást. További részleteket tekinthet meg [Windows egészségügyi jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Írja be a SAC kérdezzen rá Ha engedélyezve van a kernel hibakeresés nem sikerült | A virtuális gép, és futtassa az RDP `bcdedit /debug {current} off` egy rendszergazda jogú parancssorból. Ha Ön nem használhatja az RDP lehet helyette az operációsrendszer-lemez csatolása egy másik Azure virtuális géphez és módosítani, amíg csatlakoztatva, egy lemezt a `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, majd felcserélheti a lemez vissza.
Ha az eredeti ismétlődő jellegű rendelkezett SAC eredményez olyan harmadik karaktert a PowerShell történő beillesztéskor | A probléma megoldásához, hogy a PSReadLine modul eltávolításához. `Remove-Module PSReadLine` eltávolítja a PSReadLine modul az aktuális munkamenet.
Bizonyos billentyűzetet bemenetek állít elő kimenetet. SAC furcsa. (pl. `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) escape-karaktersorozatokat nem támogatja a SAC használatával.
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nincs egy fiók tűzfal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.

## <a name="frequently-asked-questions"></a>Gyakori kérdések 
**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése egy problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként kevesebb (elsődleges) visszajelzés küldése a keresztül azserialhelp@microsoft.com vagy a virtuális gép kategóriáját http://feedback.azure.com

**Q. Nem tudom elérni a soros konzol, ahol is fájlt egy támogatási kérést?**

A. Ezt az előzetes funkciót a hatálya alá tartozó keresztül az Azure előzetes verziójú szolgáltatás feltételeit. Támogatás a legjobban a fent említett csatornákon keresztül kezeli. 

## <a name="next-steps"></a>További lépések
* A részletes útmutatót a cmd Parancsot, és a PowerShell parancsokat is használhat, a Windows SAC, kattintson a [Itt](serial-console-cmd-ps-commands.md).
* A soros konzolon érhető el is [Linux](../linux/serial-console.md) virtuális gépeket.
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).
