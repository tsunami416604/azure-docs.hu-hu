---
title: A Windows Azure-soros konzolon |} A Microsoft Docs
description: Soros konzol és a kétirányú Azure-beli virtuális gépek és a Virtual Machine Scale Sets.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: e76fcd937f85ce3b1c156cf2f3dabb8ca95b9b68
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710553"
---
# <a name="azure-serial-console-for-windows"></a>A Windows Azure soros konzol

Az Azure Portalon a soros konzol Windows virtuális gépek (VM) egy szöveges alapú konzol hozzáférést biztosít, és a virtuális gép méretezési csoport példányaihoz. A soros kapcsolat a virtuális gép vagy a virtuális gép méretezési készlet példány, azt a hálózati vagy az operációs rendszer állapotának független való hozzáférés biztosítása a COM1 soros porton csatlakozik. A soros konzol csak lehet hozzáférni az Azure portal használatával, és csak a közreműködői hozzáférés szerepkörrel rendelkező felhasználók számára engedélyezett vagy újabb, a virtuális gép vagy virtuálisgép-méretezési csoporthoz.

Soros konzol virtuális gépek azonos módon működik, és a virtuális gép méretezési csoport példányaihoz. Ez a dokumentum a virtuális gépek összes említései implicit módon tartalmazza a virtuális gép méretezési csoport példányaihoz Ha másként nincs jelezve.

A soros konzol dokumentációja a Linux rendszerű virtuális gépek és virtuálisgép-méretezési csoportot: [soros konzol Azure Linux-](serial-console-linux.md).

> [!NOTE]
> A soros konzol szolgáltatás általánosan elérhető a globális Azure-régióban. Ez még nem érhető el az Azure government vagy Azure China felhőkben.


## <a name="prerequisites"></a>Előfeltételek

* A virtuális gép vagy a virtuális gép méretezési készlet példány kell használnia a resource management üzemi modellhez. Klasszikus üzemi modellben nem támogatottak.

- A soros konzol használó fióknak rendelkeznie kell a [virtuális gépek Közreműködője szerepkör](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiók

- A virtuális gép vagy a virtuális gép méretezési készlet példány jelszóalapú felhasználó kell rendelkeznie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény funkcióját. Válassza ki **jelszó alaphelyzetbe állítása** származó a **támogatás + hibaelhárítás** szakaszban.

* Rendelkeznie kell a virtuális gép, amelyben éri el a soros konzol [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van.

    ![Rendszerindítási diagnosztikai beállításokat](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>A soros konzol használatának első lépései
A soros konzol virtuális gépek és virtuálisgép-méretezési csoportban csak az Azure Portalon keresztül érhető el:

### <a name="serial-console-for-virtual-machines"></a>A virtuális gépek soros konzol
A virtuális gépek soros konzol olyan egyszerű, amilyennek kattint **soros konzol** belül a **támogatás + hibaelhárítás** szakaszban az Azure Portalon.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Navigáljon a **összes erőforrás** , és válasszon egy virtuális gépet. A virtuális gép megnyílik.

  1. Görgessen le a **támogatás + hibaelhárítás** szakaszt, és válassza **soros konzol**. A soros konzol segítségével egy új panel nyílik meg, és elindítja a kapcsolatot.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Soros konzol a Virtual Machine Scale Sets
Soros konzol virtuálisgép-méretezési csoportokhoz tartozó példányonként alapon érhető el. Nyissa meg egy virtuálisgép-méretezési csoportot az egyes példányához csapatához kell a **soros konzol** gombra. Ha a virtuálisgép-méretezési készlet nem rendelkezik a rendszerindítási diagnosztika engedélyezve van, győződjön meg arról, frissíti a virtuálisgép-méretezési csoport modelljét a rendszerindítási diagnosztika engedélyezése, majd utána frissítse az összes példányt az új modellre annak érdekében, hogy a soros konzol eléréséhez.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Navigáljon a **összes erőforrás** , és válasszon egy virtuálisgép-méretezési csoportban. Megnyílik a virtuálisgép-méretezési csoport áttekintő oldala megadása

  1. Navigáljon a **példányok**

  1. Válassza ki a virtuálisgép-méretezési készlet példány

  1. Az a **támogatás + hibaelhárítás** szakaszban jelölje be **soros konzol**. A soros konzol segítségével egy új panel nyílik meg, és elindítja a kapcsolatot.

## <a name="enable-serial-console-functionality"></a>Soros konzol funkció engedélyezése

> [!NOTE]
> Ha nem jelennek meg semmit a soros konzolon, ügyeljen arra, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép vagy virtuálisgép-méretezési.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>A soros konzol egyéni vagy régebbi rendszerképek engedélyezése
Az Azure-ban újabb Windows Server-rendszerképek [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezve van. SAC a Windows server-verziók esetében támogatott, de nem érhető el, az ügyfél-verziók (például a Windows 10, Windows 8 vagy Windows 7).

A régebbi Windows Server-rendszerképeket (a 2018 Februárja előtt létrehozott) a soros konzolon keresztül az Azure Portalon futtatási parancs funkciója automatikusan engedélyezheti. Az Azure Portalon válassza ki a **futtatása paranccsal**, majd válassza ki a parancs nevű **EnableEMS** a listából.

![Futtassa a parancsot listája](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Azt is megteheti a soros konzol 2018 Februárja előtt létrehozott Windows virtuális gépek/virtuális gép méretezési manuálisan engedélyezéséhez kövesse az alábbi lépéseket:

1. A Windows virtuális gép kapcsolódni a távoli asztal használatával
1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Indítsa újra a rendszer a SAC konzol engedélyezni kell.

    ![SAC konzol](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Szükség esetén a SAC is engedélyezése kapcsolat nélküli módban is:

1. A windows lemez legyen konfigurálva, a meglévő virtuális gép egy adatlemezt SAC csatlakoztatásához.

1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Honnan tudhatom meg, hogy engedélyezve van-e SAC?

Ha [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve, a soros konzol nem jeleníti meg a SAC használatával. Bizonyos esetekben a virtuális gép állapotára vonatkozó látható, és más esetben üres. Ha a 2018 Februárja előtt létrehozott Windows Server-rendszerképet használ, SAC valószínűleg nem engedélyezhető.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>A soros konzol a Windows rendszertöltő menü engedélyezése

Ha a Windows rendszertöltő betöltő utasításokat a soros konzolon megjelenítendő engedélyeznie kell, a következő beállítások is hozzáadhat a rendszerindítási konfigurációs adatok. További információkért lásd: [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Kapcsolódás a Windows virtuális gép vagy virtuálisgép-méretezési csoport példány távoli asztal használatával.

1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Indítsa újra a rendszert, engedélyezni kell a rendszerindító menü

> [!NOTE]
> Az időkorlát, a rendszerindító manager menü megjelenítéséhez beállított negatív hatással lesz az operációs rendszer rendszerindítási ideje. Ha úgy gondolja, hogy a 10 másodperces időtúllépési érték túl rövid, vagy túl hosszú, állítsa be egy másik értéket.

## <a name="use-serial-console"></a>Soros konzol használata

### <a name="use-cmd-or-powershell-in-serial-console"></a>A soros konzol CMD vagy a PowerShell használata

1. Csatlakozás soros konzolon. Sikeresen csatlakozott, a rendszer kéri-e **SAC >** :

    ![SAC csatlakozni](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Adja meg `cmd` hozhat létre egy csatornát, amely megtalálható a CMD példánya.

1.  Adja meg `ch -si 1` váltson át a csatorna, amelyen fut a CMD-példány.

1.  Nyomja meg **Enter**, majd adja meg a bejelentkezési hitelesítő adatok rendszergazdai engedélyekkel.

1.  Miután megadta az érvényes hitelesítő adatokat, megnyílik a CMD-példány.

1.  Adjon meg egy PowerShell-példány indításához `PowerShell` a CMD-példányt, és nyomja le az **Enter**.

    ![Nyissa meg a PowerShell-példány](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Használja a soros konzol NMI hívások
Egy nem maszkolható (NMI) úgy tervezték, hogy hozzon létre egy olyan jelet, hogy a szoftverek virtuális gépi nem figyelmen kívül. Hagyományosan NMIs figyelje a hardverekkel kapcsolatos problémák szerepelnek, amelyek adott válaszidők szükséges rendszereken voltak használva. Ma, programozók és a rendszer a rendszergazdák gyakran használnak NMI mechanizmusként javításához vagy hibaelhárítása a rendszerek, amelyek nem válaszolnak.

A soros konzol segítségével egy Azure-beli virtuálisgép-NMI küldje el a billentyűzet ikon használatával a parancssávon. Miután a NMI érkezik, a virtuális gép konfigurációja szabályozza hogyan reagál a rendszer. Windows beállítható úgy, hogy az összeomlási, és a memóriakép létrehozása egy NMI fogadásakor.

![NMI küldése](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Windows összeomlási memóriaképfájl létrehozásához, amikor kap egy NMI konfigurálásával kapcsolatos további információkért lásd: [egy összeomlási memóriakép létrehozása egy NMI használatával](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Függvény-kulcsok használata a soros konzol
Funkcióbillentyűk engedélyezve vannak a soros konzol a Windows virtuális gépek használatát. A soros konzol legördülő listában az F8 biztosít, egyszerűen írja be a speciális rendszerindítási beállítások menü a kényelem érdekében, de a soros konzol kompatibilis a függvény minden más kulcsok. Nyomja le az szeretne **Fn** + **F1** (vagy, F2 és F3, stb.) függően a számítógépen a billentyűzet a soros konzol használata.

### <a name="use-wsl-in-serial-console"></a>A soros konzol WSL használata
A Windows alrendszer Linux (WSL) engedélyezve van a Windows Server 2019 vagy újabb verzió, így is lehet engedélyezni WSL soros konzolon belüli használatra, ha futtatja a Windows Server 2019 vagy újabb verziója. Egy Linux-parancsok ismeretét rendelkező felhasználók számára előnyös lehet. A Windows Server WSL engedélyezéséhez utasításokért lásd: a [telepítési útmutató](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>A Windows virtuális gép/virtuális gép méretezési set-példány újraindítása soros konzolról
A soros konzolból újraindítást kezdeményezhet főkapcsoló részen, majd kattintson a "Virtuális gép újraindítása". A szolgáltatás kezdeményez a virtuális gép újraindítása, és megjelenik egy értesítés az Azure Portalon az újraindítás kapcsolatban.

Ez hasznos olyan helyzetben, amikor lehetséges, hogy a rendszerindító menü eléréséhez a soros konzol élmény elhagyása nélkül.

![Windows soros konzol újraindítása](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Tiltsa le a soros konzol
Alapértelmezés szerint minden előfizetés rendelkezik a soros konzol hozzáférés engedélyezve van az összes virtuális gép. A soros konzol vagy az előfizetés vagy a virtuális gép szintjén is letilthatja.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Virtuális gép/virtuális gép scale set-szintű letiltása
A soros konzol egy adott virtuális gép vagy virtuálisgép-méretezési csoportot a rendszerindítási diagnosztikai beállítás letiltásával lehet letiltani. Kapcsolja ki a rendszerindítási diagnosztika letiltásához a soros konzol a virtuális gép vagy a virtuálisgép-méretezési csoportot az Azure Portalról. Ha a soros konzol egy virtuálisgép-méretezési csoportot használ, győződjön meg arról, a legújabb modellre való frissítése a virtuális gép méretezési csoport példányaihoz.

> [!NOTE]
> Engedélyezi vagy letiltja a soros konzol-előfizetéssel, az előfizetés írási engedélyekkel rendelkeznie. Ezek az engedélyek közé tartozik, de nem kizárólagosan, a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is lehet írási engedéllyel.

### <a name="subscription-level-disable"></a>Előfizetés-szintű letiltása
A soros konzolon keresztül egy teljes előfizetésre letiltható a [tiltsa le a konzolon REST API-hívás](/rest/api/serialconsole/console/disableconsole). Ez a művelet közreműködője szintű hozzáférésre van szüksége, vagy a fenti az előfizetéshez. Használhatja a **Kipróbálom** funkció letiltása és engedélyezése a soros konzol egy előfizetés az API dokumentációja oldalon érhető el. Adja meg az előfizetés-azonosítója **subscriptionId**, adja meg "alapértelmezett" **alapértelmezett**, majd válassza ki **futtatása**. Az Azure CLI-parancsok még nem érhetők el.

Soros konzol egy előfizetés újbóli engedélyezéséhez használja a [engedélyezése konzol REST API-hívás](/rest/api/serialconsole/console/enableconsole).

![REST API-t próbálja ki](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Másik lehetőségként használhatja az alábbi bash-parancsok készletét a Cloud Shellben letiltása, engedélyezése és a soros konzol egy előfizetés letiltott állapotának megtekintése:

* Előfizetés a soros konzol letiltott állapotának lekéréséhez:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* A soros konzol egy előfizetés letiltása:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* A soros konzol egy előfizetés engedélyezése:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Soros konzol biztonsága

### <a name="access-security"></a>Hozzáférés-biztonságot
A soros konzoljához való hozzáférés hozzáférési szerepkörrel rendelkező felhasználók korlátozódik, [virtuális gépek Közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy újabb, a virtuális géphez. Ha az Azure Active Directory-bérlő a multi-factor authentication (MFA) szükséges, akkor a soros konzoljához való hozzáférés is kell többtényezős hitelesítés, mert a soros konzol fér hozzá a [az Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Csatorna biztonsági
Hálózati oda-vissza küldött összes adatot titkosított.

### <a name="audit-logs"></a>Naplók
A soros konzoljához való hozzáférés teljes jelenleg be van jelentkezve a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure-beli virtuálisgép-rendszergazda határozza meg.

> [!CAUTION]
> A konzol nem hozzáférést jelszavát a rendszer naplózza. Azonban a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket, vagy bármely más személyes azonosításra alkalmas adatok (PII) kimeneti, ha azok lesz írva az a virtuális gép rendszerindítási diagnosztikai naplók. Ezek lesz írva az összes többi látható szöveg, valamint a soros konzolon görgessen vissza végrehajtásának részeként függvény. Ezeket a naplókat. kör alakú és egyetlen egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező tudja elérni őket. Azonban javasoljuk, hogy kövesse az ajánlott eljárás szerint az összes adat távoli asztallal, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok.

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó megszakítja, és a második felhasználó csatlakozik-e ugyanabban a munkamenetben.

> [!CAUTION]
> Ez azt jelenti, hogy le van választva a felhasználó nem kijelentkeztetjük. Képes érvényesíteni a kijelentkezési kapcsolat bontása esetén (használatával SIGHUP vagy hasonló mechanizmus) továbbra is szerepel az ütemterv részét képezi. A Windows van egy automatikus időtúllépés, SAC; engedélyezve Linux esetén konfigurálhatja úgy a terminál időkorlátozási beállítást.

## <a name="accessibility"></a>Kisegítő lehetőségek
Kisegítő lehetőségek egy kulcsfontosságú fókuszának az Azure-soros konzolon. Ebből a célból keresztülhaladnak, hogy a soros konzolon érhető el a visual és szolgálhattunk korlátozott, valamint azokat, akik nem feltétlenül tudja az egérrel.

### <a name="keyboard-navigation"></a>Billentyűzetnavigációt
Használja a **lapon** billentyűt a billentyűzeten a soros konzol felületen az Azure Portalról nyissa meg. A hely kiemelten jelenik meg a képernyőn. Hagyja a lépéseknek az ismertetése, a soros konzol ablakából, nyomja le a **Ctrl**+**F6** a billentyűzeten.

### <a name="use-the-serial-console-with-a-screen-reader"></a>A soros konzol használata a képernyőolvasó
A soros konzol rendelkezik beépített képernyőolvasó támogatása. Kapcsolva képernyőolvasóval való navigálni lehetővé teszi a helyettesítő szöveget a jelenleg kijelölt gombra kattintva a képernyőolvasó felolvassa olvasni.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>A soros konzol eléréséhez a gyakori forgatókönyvek

Forgatókönyv          | A soros konzol műveletek
:------------------|:-----------------------------------------
Helytelen tűzfal-szabályok | Soros konzolon, és javítsa a Windows tűzfalszabályok eléréséhez.
Fájlrendszer sérülése és ellenőrzése | A soros konzol eléréséhez, és a fájlrendszer helyreállítani.
RDP-konfigurációs problémák | A soros konzol eléréséhez, és módosítsa a beállításokat. További információkért lásd: a [RDP-dokumentáció](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Rendszer hálózati zárolása | A soros konzol eléréséhez a rendszer kezelését az Azure Portalról. Egyes hálózati parancsok felsorolt [Windows parancsokat: CMD és a PowerShell](serial-console-cmd-ps-commands.md).
A rendszertöltő használata | Hozzáférés a BCD soros konzolon keresztül. További információ: [engedélyezése a soros konzol a Windows rendszerindítási menüjében](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Hibák
Mivel a legtöbb hiba átmeneti, a kapcsolat újrapróbálása milyen gyakran oldhatja meg őket. Az alábbi táblázat azon hibákat és azok mérséklési lehetőségeit mindkét virtuális gép listáját, és a virtuális gép méretezési csoport példányaihoz.

Hiba                            |   Kezelés
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait  *&lt;VMNAME&gt;* . A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van.
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. | Virtuális gépnek kell lennie a soros konzol eléréséhez a elindított állapotú
Nem rendelkezik a virtuális gép soros konzol használatához a szükséges engedélyekkel. Győződjön meg arról, hogy a virtuális gépek Közreműködője szerepkör engedélyei.| Soros hozzáférés bizonyos engedélyekre van szüksége. További információkért lásd: [Előfeltételek](#prerequisites).
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának  *&lt;STORAGEACCOUNTNAME&gt;* . Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Soros hozzáférés bizonyos engedélyekre van szüksége. További információkért lásd: [Előfeltételek](#prerequisites).
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nincs egy fiók tűzfal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.
Web socket le van zárva, vagy nem nyitható meg. | Szükség lehet az engedélyezési listára `*.console.azure.com`. Egy részletesebb, de hosszabb megközelítés az engedélyezési listára a [a Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653), amelyek viszonylag sűrűn változnak.
Csak az egészségügyi információk Windows virtuális Géphez való csatlakozáskor jelenik meg| Ez a hiba akkor fordul elő, ha a speciális felügyeleti konzol nincs engedélyezve a Windows-lemezkép. Lásd: [engedélyezése a soros konzol egyéni vagy régebbi képeken](#enable-the-serial-console-in-custom-or-older-images) manuálisan engedélyezni a virtuális Gépen Windows SAC létrehozásával kapcsolatos útmutatást. További információkért lásd: [Windows egészségügyi jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Ismert problémák
A soros konzol problémák tisztában vagyunk. Ezekről a problémákról és kockázatcsökkentési lépések listáját itt látható. Ezekről a problémákról és megoldások a alkalmazni mindkét virtuális gép számára, és a virtuális gép méretezési csoport példányaihoz.

Probléma                             |   Kezelés
:---------------------------------|:--------------------------------------------|
Billentyű **Enter** után a kapcsolaton transzparens, nem váltják ki a bejelentkezési kérések jelenhetnek megjeleníteni. | További információkért lásd: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a hiba akkor fordulhat elő, ha egy egyéni virtuális Gépet, megerősített készülék vagy rendszerindítási konfiguráció, amelynek hatására a Windows a soros port megfelelően csatlakozni sikertelen futtatja. Ez a hiba fog is előfordulhat, ha futtatja a Windows 10-es ügyfél virtuális Géphez, mert engedélyezve van az EMS csak a Windows Server virtuális gépek vannak konfigurálva.
Írja be a SAC kérdezzen rá Ha engedélyezve van a kernel hibakeresés nem. | A virtuális gép, és futtassa az RDP `bcdedit /debug {current} off` egy rendszergazda jogú parancssorból. Ha nem használhatja az RDP, akkor ehelyett az operációsrendszer-lemez csatolása egy másik Azure virtuális géphez és módosítsa, amíg a futó adatlemezként csatlakoztatott `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, majd felcserélése vissza a lemezt.
Beillesztése PowerShell rendszerbe SAC eredményez olyan harmadik karaktert, ha az eredeti rendelkezett ismétlődő karaktert. | A probléma megoldásához futtassa `Remove-Module PSReadLine` való eltávolítása a PSReadLine modul az aktuális munkamenet. Ez a művelet nem törli vagy eltávolítja a modult.
Bizonyos billentyűzetet bemenetek furcsa SAC kimeneti előállításához (például **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) escape-karaktersorozatokat a SAC használatával által nem támogatott.
Illessze be a hosszú karakterláncok nem működik. | A soros konzol illeszthetők be a terminál 2048 karakter hosszúságú lehet, megelőzve a soros port sávszélesség sztring hossza korlátozza.
Soros konzol nem működik egy storage-fiók tűzfal. | Soros konzol szándékosan nem képes együttműködni az engedélyezve a rendszerindítás-diagnosztikai tárfiók a storage-fiók tűzfalak.
Soros konzol nem működik a storage-fiók az Azure Data Lake Storage Gen2 a hierarchikus névterek. | Ez a hierarchikus névterek egy ismert hibája. Megoldásához, győződjön meg arról, hogy a virtuális gép rendszerindítási diagnosztika tárfiókja nem jön létre az Azure Data Lake Storage Gen2 használatával. Ez a beállítás csak akkor állítható tárfiók létrehozása után. Előfordulhat, hogy egy külön a rendszerindítási diagnosztika tárfiók létrehozása az Azure Data Lake Storage Gen2 engedélyezve van probléma megoldásához nélkül.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés a githubon található létrehozásával https://aka.ms/serialconsolefeedback. Másik lehetőségként (kevésbé elsődleges), visszajelzést küldhet keresztül azserialhelp@microsoft.com vagy virtuálisgép-kategóriába https://feedback.azure.com.

**Q. Támogatja a soros konzol másolja és illessze be?**

A. Igen. Használat **Ctrl**+**Shift**+**C** és **Ctrl**+**Shift** + **V** másolja és illessze be a terminálba.

**Q. Ki engedélyezheti vagy letilthatja a soros konzol az előfizetésemet?**

A. Engedélyezi vagy letiltja a soros konzol egy előfizetési szintű szintjén, az előfizetés írási engedélyekkel rendelkeznie. Írási engedéllyel rendelkező szerepek közé tartozik a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is lehet írási engedéllyel.

**Q. Virtuális gép férhet hozzá a soros konzol?**

A. A virtuális gép közreműködő szerepkörrel kell rendelkeznie legalább egy virtuális géphez a virtuális gép soros konzol eléréséhez.

**Q. A soros konzol nem jelennek meg, mi a teendő?**

A. A rendszerkép valószínűleg hibásan konfigurált, soros hozzáféréshez. A lemezkép engedélyezése a soros konzol konfigurálásával kapcsolatos további információkért lásd: [engedélyezése a soros konzol egyéni vagy régebbi képeken](#enable-the-serial-console-in-custom-or-older-images).

**Q. A soros konzolon érhető el a virtuálisgép-méretezési csoportok?**

A. Jelenleg a virtuális gép méretezési csoport példányaihoz a soros konzoljához való hozzáférés nem támogatott.

## <a name="next-steps"></a>További lépések
* A részletes útmutatót a cmd Parancsot, és a PowerShell parancsokat is használhat, a Windows SAC, lásd: [Windows parancsokat: CMD és a PowerShell](serial-console-cmd-ps-commands.md).
* A soros konzolon érhető el is [Linux](serial-console-linux.md) virtuális gépeket.
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).
