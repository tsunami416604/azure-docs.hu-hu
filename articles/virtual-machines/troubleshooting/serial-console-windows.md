---
title: Az Azure virtuális gépek soros konzolja a Windows |} A Microsoft Docs
description: Azure Windows virtuális gépek soros konzol és a kétirányú.
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
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 61b64b63a53318e0a703678d5525399fe13efa83
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432762"
---
# <a name="virtual-machine-serial-console-for-windows"></a>A Windows virtuális gépek soros konzolja

Az Azure Portalon a virtuális gép (VM) soros konzol egy szöveges alapú konzol Windows virtuális gépek számára hozzáférést biztosít. A soros kapcsolat a virtuális gép, azt a virtuális gép hálózati vagy az operációs rendszer állapotának független való hozzáférés biztosítása a COM1 soros porton csatlakozik. Virtuális gép a soros konzoljához való hozzáférés csak az Azure portal használatával hajtható végre. Ez csak a virtuális géphez a virtuális gépek Közreműködője vagy magasabb hozzáférési szerepkörrel rendelkező felhasználók engedélyezett.

Linux rendszerű virtuális gépek soros konzol dokumentációjáért lásd: [linuxos virtuális gépek soros konzolja](serial-console-linux.md).

> [!NOTE]
> Globális Azure-régiókban található virtuális gépek a soros konzol szolgáltatás általánosan elérhető. Ez még nem érhető el az Azure government vagy Azure China felhőkben.


## <a name="prerequisites"></a>Előfeltételek

* A virtuális gép, amelyben a soros konzol éri el a resource management üzemi modellhez kell használnia. Klasszikus üzemi modellben nem támogatottak.

* Rendelkeznie kell a virtuális gép, amelyben éri el a soros konzol [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van.

    ![Rendszerindítási diagnosztikai beállításokat](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Rendelkeznie kell egy fiókot a soros konzol használatával a [virtuális gépek Közreműködője szerepkör](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiókot.

* A virtuális gép, amelyben éri el a soros konzol jelszóalapú fiókkal kell rendelkeznie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény funkcióját. Válassza ki **jelszó alaphelyzetbe állítása** származó a **támogatás + hibaelhárítás** szakaszban.


## <a name="get-started-with-the-serial-console"></a>A soros konzol használatának első lépései
A soros konzol virtuális gépek csak az Azure Portalon keresztül érhető el:

  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
  1. A bal oldali menüben válassza ki a **virtuális gépek**.
  1. Válassza ki egy virtuális Gépet a listából. A virtuális gép áttekintő oldala nyílik meg.
  1. Görgessen le a **támogatás + hibaelhárítás** szakaszt, és válassza **soros konzol**. A soros konzol segítségével egy új panel nyílik meg, és elindítja a kapcsolatot.

## <a name="enable-serial-console-functionality"></a>Soros konzol funkció engedélyezése

> [!NOTE]
> Ha semmit a soros konzol nem láthatóak, ügyeljen arra, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális Gépen.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>A soros konzol egyéni vagy régebbi rendszerképek engedélyezése
Az Azure-ban újabb Windows Server-rendszerképek [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezve van. SAC a Windows server-verziók esetében támogatott, de nem érhető el, az ügyfél-verziók (például a Windows 10, Windows 8 vagy Windows 7).

A régebbi Windows Server-rendszerképeket (a 2018 Februárja előtt létrehozott) a soros konzolon keresztül az Azure Portalon futtatási parancs funkciója automatikusan engedélyezheti. Az Azure Portalon válassza ki a **futtatása paranccsal**, majd válassza ki a parancs nevű **EnableEM** a listából.

![Futtassa a parancsot listája](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Azt is megteheti a soros konzol a 2018 Februárja előtt létrehozott Windows virtuális gépek manuális engedélyezéséhez kövesse az alábbi lépéseket:

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

1. A Windows virtuális gép kapcsolódni a távoli asztal használatával.

1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Indítsa újra a rendszert, engedélyezni kell a rendszerindító menü

> [!NOTE]
> Az időkorlát, a rendszerindító manager menü megjelenítéséhez beállított negatív hatással lesz az operációs rendszer rendszerindítási ideje. Ha úgy gondolja, hogy a 10 másodperces időtúllépési érték túl rövid, vagy túl hosszú, állítsa be egy másik értéket.

## <a name="use-serial-console"></a>Soros konzol használata

### <a name="use-cmd-or-powershell-in-serial-console"></a>A soros konzol CMD vagy a PowerShell használata

1. Csatlakozás soros konzolon. Sikeresen csatlakozott, a rendszer kéri-e **SAC >**:

    ![SAC csatlakozni](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Adja meg `cmd` hozhat létre egy csatornát, amely megtalálható a CMD példánya.

1.  Adja meg `ch -si 1` váltson át a csatorna, amelyen fut a CMD-példány.

1.  Nyomja meg **Enter**, majd adja meg a bejelentkezési hitelesítő adatok rendszergazdai engedélyekkel.

1.  Miután megadta az érvényes hitelesítő adatokat, megnyílik a CMD-példány.

1.  Adjon meg egy PowerShell-példány indításához `PowerShell` a CMD-példányt, és nyomja le az **Enter**.

    ![Nyissa meg a PowerShell-példány](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Használja a soros konzol NMI hívások
Egy nem maszkolható (NMI) úgy tervezték, hogy hozzon létre egy olyan jelet, hogy a szoftverek virtuális gépi nem figyelmen kívül. Hagyományosan NMIs figyelje a hardverekkel kapcsolatos problémák szerepelnek, amelyek adott válaszidők szükséges rendszereken voltak használva. Ma, programozók és a rendszergazdák számára a NMI gyakran használnak, hibakeresése és hibaelhárítást mechanizmussal rendszerek indításkor lefagyott.

A soros konzol segítségével egy Azure-beli virtuálisgép-NMI küldje el a billentyűzet ikon használatával a parancssávon. Miután a NMI érkezik, a virtuális gép konfigurációja szabályozza hogyan reagál a rendszer. Windows beállítható úgy, hogy az összeomlási, és a memóriakép létrehozása egy NMI fogadásakor.

![NMI küldése](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Windows összeomlási memóriaképfájl létrehozásához, amikor kap egy NMI konfigurálásával kapcsolatos további információkért lásd: [egy összeomlási memóriakép létrehozása egy NMI használatával](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Függvény-kulcsok használata a soros konzol
Funkcióbillentyűk engedélyezve vannak a soros konzol a Windows virtuális gépek használatát. A soros konzol legördülő listában az F8 biztosít, egyszerűen írja be a speciális rendszerindítási beállítások menü a kényelem érdekében, de a soros konzol kompatibilis a függvény minden más kulcsok. Nyomja le az szeretne **Fn** + **F1** (vagy, F2 és F3, stb) függően a számítógépen a billentyűzet a soros konzol használata.

### <a name="use-wsl-in-serial-console"></a>A soros konzol WSL használata
A Windows alrendszer Linux (WSL) engedélyezve van a Windows Server 2019 vagy újabb verzió, így is lehet engedélyezni WSL soros konzolon belüli használatra, ha futtatja a Windows Server 2019 vagy újabb verziója. Egy Linux-parancsok ismeretét rendelkező felhasználók számára előnyös lehet. A Windows Server WSL engedélyezéséhez utasításokért lásd: a [telepítési útmutató](https://docs.microsoft.com/windows/wsl/install-on-server).

## <a name="disable-serial-console"></a>Tiltsa le a soros konzol
Alapértelmezés szerint minden előfizetés rendelkezik a soros konzol hozzáférés engedélyezve van az összes virtuális gép. A soros konzol vagy az előfizetés vagy a virtuális gép szintjén is letilthatja.

> [!NOTE]
> Engedélyezi vagy letiltja a soros konzol-előfizetéssel, az előfizetés írási engedélyekkel rendelkeznie. Ezek az engedélyek közé tartozik, de nem kizárólagosan, a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is lehet írási engedéllyel.

### <a name="subscription-level-disable"></a>Előfizetés-szintű letiltása
A soros konzolon keresztül egy teljes előfizetésre letiltható a [tiltsa le a konzolon REST API-hívás](/rest/api/serialconsole/console/disableconsole). Használhatja a **Kipróbálom** funkció letiltása és engedélyezése a soros konzol egy előfizetés az API dokumentációja oldalon érhető el. Adja meg az előfizetés-azonosítója **subscriptionId**, adja meg "alapértelmezett" **alapértelmezett**, majd válassza ki **futtatása**. Az Azure CLI-parancsok még nem érhetők el.

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

### <a name="vm-level-disable"></a>Virtuálisgép-szintű letiltása
A soros konzol az adott virtuális gép rendszerindítási diagnosztikai beállítás letiltásával letiltható egy adott virtuális gép. Kapcsolja ki az Azure Portalon, tiltsa le a soros konzol a virtuális gép rendszerindítási diagnosztikája.

## <a name="serial-console-security"></a>Soros konzol biztonsága

### <a name="access-security"></a>Hozzáférés-biztonságot
A soros konzoljához való hozzáférés hozzáférési szerepkörrel rendelkező felhasználók korlátozódik, [virtuális gépek Közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy újabb, a virtuális géphez. Ha az Azure Active Directory-bérlő a multi-factor authentication (MFA) szükséges, akkor a soros konzoljához való hozzáférés is kell többtényezős hitelesítés, mert a soros konzol fér hozzá a [az Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Csatorna biztonsági
Hálózati oda-vissza küldött összes adatot titkosított.

### <a name="audit-logs"></a>Naplók
A soros konzoljához való hozzáférés teljes jelenleg be van jelentkezve a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure-beli virtuálisgép-rendszergazda határozza meg.

>[!CAUTION]
A konzol nem hozzáférést jelszavát a rendszer naplózza. Azonban a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket, vagy bármely más személyes azonosításra alkalmas adatok (PII) kimeneti, ha azok lesz írva az a virtuális gép rendszerindítási diagnosztikai naplók. Ezek lesz írva az összes többi látható szöveg, valamint a soros konzolon görgessen vissza végrehajtásának részeként függvény. Ezeket a naplókat. kör alakú és egyetlen egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező tudja elérni őket. Azonban javasoljuk, hogy kövesse az ajánlott eljárás szerint az összes adat távoli asztallal, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok.

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó megszakítja, és a második felhasználó csatlakozik-e ugyanabban a munkamenetben.

>[!CAUTION]
Ez azt jelenti, hogy le van választva a felhasználó nem kijelentkeztetjük. Képes érvényesíteni a kijelentkezési kapcsolat bontása esetén (használatával SIGHUP vagy hasonló mechanizmus) továbbra is szerepel az ütemterv részét képezi. A Windows van egy automatikus időtúllépés, SAC; engedélyezve Linux esetén konfigurálhatja úgy a terminál időkorlátozási beállítást.

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
Mivel a legtöbb hiba átmeneti, a kapcsolat újrapróbálása milyen gyakran oldhatja meg őket. Az alábbi táblázat azon hibákat és kezelési lehetőségeiket listáját.

Hiba                            |   Kezelés
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait  *&lt;VMNAME&gt;*. A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van.
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. | Virtuális gépnek kell lennie a soros konzol eléréséhez a elindított állapotú
Nem rendelkezik a virtuális gép soros konzol használatához a szükséges engedélyekkel. Győződjön meg arról, hogy a virtuális gépek Közreműködője szerepkör engedélyei.| Soros hozzáférés bizonyos engedélyekre van szüksége. További információkért lásd: [Előfeltételek](#prerequisites).
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának  *&lt;STORAGEACCOUNTNAME&gt;*. Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Soros hozzáférés bizonyos engedélyekre van szüksége. További információkért lásd: [Előfeltételek](#prerequisites).
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nincs egy fiók tűzfal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.
Web socket le van zárva, vagy nem nyitható meg. | Szükség lehet az engedélyezési listára `*.console.azure.com`. Egy részletesebb, de hosszabb megközelítés az engedélyezési listára a [a Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653), amelyek viszonylag sűrűn változnak.
Csak az egészségügyi információk Windows virtuális Géphez való csatlakozáskor jelenik meg| Ez a hiba akkor fordul elő, ha a speciális felügyeleti konzol nincs engedélyezve a Windows-lemezkép. Lásd: [engedélyezése a soros konzol egyéni vagy régebbi képeken](#enable-the-serial-console-in-custom-or-older-images) manuálisan engedélyezni a virtuális Gépen Windows SAC létrehozásával kapcsolatos útmutatást. További információkért lásd: [Windows egészségügyi jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Ismert problémák
A soros konzol problémák tisztában vagyunk. Ezekről a problémákról és kockázatcsökkentési lépések listáját itt látható.

Probléma                             |   Kezelés
:---------------------------------|:--------------------------------------------|
Billentyű **Enter** után a kapcsolaton transzparens, nem váltják ki a bejelentkezési kérések jelenhetnek megjeleníteni. | További információkért lásd: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a hiba akkor fordulhat elő, ha egy egyéni virtuális Gépet, megerősített készülék vagy rendszerindítási konfiguráció, amelynek hatására a Windows a soros port megfelelően csatlakozni sikertelen futtatja. Ez a hiba fog is előfordulhat, ha futtatja a Windows 10-es ügyfél virtuális Géphez, mert engedélyezve van az EMS csak a Windows Server virtuális gépek vannak konfigurálva.
Írja be a SAC kérdezzen rá Ha engedélyezve van a kernel hibakeresés nem. | A virtuális gép, és futtassa az RDP `bcdedit /debug {current} off` egy rendszergazda jogú parancssorból. Ha nem használhatja az RDP, akkor ehelyett az operációsrendszer-lemez csatolása egy másik Azure virtuális géphez és módosítsa, amíg a futó adatlemezként csatlakoztatott `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, majd felcserélése vissza a lemezt.
Beillesztése PowerShell rendszerbe SAC eredményez olyan harmadik karaktert, ha az eredeti rendelkezett ismétlődő karaktert. | A probléma megoldásához futtassa `Remove-Module PSReadLine` való eltávolítása a PSReadLine modul az aktuális munkamenet. Ez a művelet nem törli vagy eltávolítja a modult.
Bizonyos billentyűzetet bemenetek furcsa SAC kimeneti előállításához (például **[A**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) escape-karaktersorozatokat a SAC használatával által nem támogatott.
Illessze be a hosszú karakterláncok nem működik. | A soros konzol illeszthetők be a terminál 2048 karakter hosszúságú lehet, megelőzve a soros port sávszélesség sztring hossza korlátozza.
Soros konzol nem működik egy storage-fiók tűzfal. | Soros konzol szándékosan nem képes együttműködni az engedélyezve a rendszerindítás-diagnosztikai tárfiók a storage-fiók tűzfalak.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés a githubon található létrehozásával https://aka.ms/serialconsolefeedback. Másik lehetőségként (kevésbé elsődleges), visszajelzést küldhet keresztül azserialhelp@microsoft.com vagy virtuálisgép-kategóriába http://feedback.azure.com.

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
