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
ms.openlocfilehash: ee42c279abaf9282b6f37e2b00050d33ebd093d2
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318265"
---
# <a name="virtual-machine-serial-console"></a>Virtuális gépek soros konzolja


Az Azure-beli virtuális gépek soros konzolja egy szöveges alapú konzol Windows virtuális gépek számára hozzáférést biztosít. A soros kapcsolat, hogy a virtuális gép, a virtuális gép, amely független a virtuális gépek hálózati vagy operációs rendszer állapotának való hozzáférés biztosítása a COM1 soros port. A soros konzol elérhető virtuális gépet is jelenleg csak az Azure Portalon keresztül történik, és csak azok számára, akik rendelkeznek a virtuális gép közreműködő vagy újabb a virtuális géphez való hozzáférés engedélyezett. 

A soros konzol dokumentáció Linux rendszerű virtuális gépekhez [ide](serial-console-linux.md).

> [!NOTE] 
> Globális Azure-régiókban található virtuális gépek soros konzol szolgáltatás általánosan elérhető. Ezen a ponton soros konzol még nem érhető el az Azure Government vagy Azure China-felhőkben.

 

## <a name="prerequisites"></a>Előfeltételek 

* A resource management üzemi modellhez kell használnia. Klasszikus üzemi modellben nem támogatottak. 
* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* A soros konzol használatával a fióknak rendelkeznie kell [közreműködői szerepkört](../../role-based-access-control/built-in-roles.md) virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiókot. 
* A virtuális gép, amelyekhez le a soros konzol acessing is jelszóalapú fiókkal kell rendelkeznie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény - funkciójának lásd az alábbi képernyőképet.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Soros konzol használatának első lépései
A virtuális gépek soros konzolon keresztül csak érhető el [az Azure portal](https://portal.azure.com). Az alábbiakban a lépéseket a portálon keresztül a virtuális gépek a soros konzol eléréséhez.

  1. Nyissa meg az Azure Portalon
  2. A bal oldali menüben válassza ki a virtuális gépeket.
  3. Kattintson a listában a virtuális gépen. A virtuális gép áttekintő oldala nyílik meg.
  4. Görgessen le a támogatás és hibaelhárítás szakaszhoz, és kattintson a "Soros konzol" lehetőséget. A soros konzol segítségével egy új panel megnyitja és indítsa el a kapcsolatot.


## <a name="enable-serial-console-in-custom-or-older-images"></a>Soros konzol engedélyezése egyéni vagy régebbi képeken
Újabb Windows Server-rendszerképeket az Azure-ban kell [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezve van. SAC a Windows server-verziók esetében támogatott, de nem áll rendelkezésre az ügyfél-verziók (például a Windows 10, Windows 8 vagy Windows 7). Ahhoz, hogy a 2018 Februárja előtt létrehozott Windows virtuális gépek soros konzolon, használja az alábbi lépéseket: 

1. Csatlakozás a Windows virtuális géphez a távoli asztalon keresztül
2. Egy rendszergazdai parancssorból a következő parancsok futtatásával. 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Indítsa újra a rendszert a SAC konzol engedélyezni kell

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Szükség esetén a SAC is engedélyezése kapcsolat nélküli módban is:

1. SAC adatlemezként meglévő virtuális géphez konfigurált kívánt windows-lemez csatolása. 
2. Egy rendszergazdai parancssorból a következő parancsok futtatásával. 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Honnan tudhatom meg, hogy engedélyezve van-e SAC?

Ha a [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve a soros konzol nem jelenik meg a SAC használatával. Bizonyos esetekben a virtuális gép állapotára vonatkozó jelennek meg, és más esetben üres lesz. Ha a 2018 Februárja előtt létrehozott Windows Server-rendszerképet használ, SAC valószínűleg nincs engedélyezve lesz.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>A Windows rendszertöltő menü soros konzolon engedélyezése 

Ha a Windows rendszertöltő engedélyeznie kell a rendszerindítási konfigurációs adatok adhat hozzá az alábbi további beállítások megjelenítéséhez a soros konzol kéri. Lásd: [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) további részletekért

1. Csatlakozás a Windows virtuális géphez a távoli asztalon keresztül
2. Egy rendszergazdai parancssorból a következő parancsok futtatásával. 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 10`
* `bcdedit /set {bootmgr} bootems yes`
3. Indítsa újra a rendszert, engedélyezni kell a rendszerindító menü

> [!NOTE] 
> Az időkorlát, a rendszerindító manager menü jelenik meg a beállított negatív hatással lesz az operációs rendszer rendszerindítási ideje a jövőben. Egyes hozzáadni, győződjön meg arról, hogy a rendszertöltés-vezérlő látható soros konzolon keresztül 10 második időtúllépés elfogadható lehet, míg mások érdemes lehet egy rövidebb vagy hosszabb időkorlát. Időtúllépési értéke teljesebb értéket.

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Használja a soros konzol NMI hívások Windows-beli virtuális gépeken
Egy nem maszkolható (NMI) úgy tervezték, hogy hozzon létre egy olyan jelet, hogy a szoftverek virtuális gépi nem figyelmen kívül hagyja. Hagyományosan NMIs figyelje a hardverekkel kapcsolatos problémák szerepelnek, amelyek adott válaszidők szükséges rendszereken voltak használva.  Ma, programozók és a rendszer a rendszergazdák gyakran használnak NMI mechanizmusként javításához vagy hibaelhárítása a rendszerek, amelyek leáll.

A soros konzol segítségével egy NMI küldeni egy Azure virtuális gépen a billentyűzet ikon használatával az alább látható a parancssávon. Miután a NMI érkezik, a virtuális gép konfigurációja szabályozza hogyan reagál a rendszer. Windows beállítható úgy, hogy az összeomlási, és a memóriakép létrehozása egy NMI fogadásakor.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Windows egy összeomlási memóriakép létrehozása egy NMI kap való konfigurálásával kapcsolatos információkért lásd: [egy teljes összeomlási memóriakép-fájl vagy a kernel összeomlási memóriakép létrehozása egy NMI egy Windows-alapú rendszeren való használatával](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="disable-serial-console"></a>Tiltsa le a soros konzol
Alapértelmezés szerint minden előfizetés rendelkezik a soros konzol hozzáférés engedélyezve van az összes virtuális gép. Soros konzol vagy az előfizetés szintjén, vagy a virtuális gép szintjén letiltható.

> [!NOTE]       
> Annak érdekében, hogy engedélyezi vagy letiltja a soros konzol-előfizetéssel, az előfizetés írási engedéllyel kell rendelkeznie. Ez magában foglalja, de a rendszergazda vagy tulajdonos szerepkörök nem korlátozódik. Egyéni szerepkörök is rendelkezhetnek írási jogosultsággal.

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

## <a name="common-scenarios-for-accessing-serial-console"></a>Általános forgatókönyvek a soros konzol eléréséhez 
Forgatókönyv          | A soros konzol műveletek                
:------------------|:-----------------------------------------
Helytelen tűzfal-szabályok | Soros konzolon, és javítsa a Windows tűzfalszabályok eléréséhez. 
Fájlrendszer sérülése és ellenőrzése | Soros konzol eléréséhez, és a fájlrendszer helyreállítani. 
RDP-konfigurációs problémák | Soros konzol eléréséhez, és módosíthatja a beállításait. Nyissa meg a [RDP-dokumentáció](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) a kezdéshez.
Rendszer hálózati zárolása| Hozzáférés soros konzolon kezelheti a rendszer a portálon keresztül. Egyes hálózati parancsok láthatók a [soros konzolon CMD és a PowerShell dokumentációs](serial-console-cmd-ps-commands.md). 
A rendszertöltő használata | Hozzáférés a BCD soros konzolon keresztül. Lépjen a [engedélyezése rendszerindító menü megjelenítése a soros konzol](#enabling-boot-menu-to-show-in-the-serial-console) a kezdéshez. 

## <a name="accessibility"></a>Kisegítő lehetőségek
Kisegítő lehetőségek egy kulcsfontosságú fókuszának az Azure-soros konzolon. Ebből a célból hogy gondoskodott róla, hogy a soros konzolon érhető el a vizualizációt és Várjuk visszajelzését látási, valamint azokat, akik nem tudják az egérrel rendelkezők.

### <a name="keyboard-navigation"></a>Billentyűzetnavigációt
Használja a `tab` billentyűt a billentyűzeten történő navigációhoz a soros konzol felület az Azure Portalon. A hely kiemelten jelenik meg a képernyőn. A lépéseknek az ismertetése, a soros konzol panelen hagyja, nyomja le a `Ctrl + F6` a billentyűzeten.

### <a name="use-serial-console-with-a-screen-reader"></a>A képernyőolvasó soros konzol használata
Soros konzol tartalmaz beépített képernyőolvasó támogatása. Kapcsolva képernyőolvasóval való navigálni lehetővé teszi a helyettesítő szöveget a jelenleg kijelölt gombra kattintva a képernyőolvasó felolvassa olvasni.

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
Csak az egészségügyi információk Windows virtuális Géphez való csatlakozáskor jelenik meg| Ez fog megjelenni Ha a speciális felügyeleti konzol nincs engedélyezve a Windows-lemezkép számára. Lásd: [hozzáférés soros konzol a Windows](#access-serial-console-for-windows) manuálisan engedélyezni a virtuális Gépen Windows SAC létrehozásával kapcsolatos útmutatást. További részleteket tekinthet meg [Windows egészségügyi jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Ismert problémák 
A soros konzol problémák ismerjük. Ezekről a problémákról és kockázatcsökkentési lépések listáját itt látható.

Probléma                             |   Kezelés 
:---------------------------------|:--------------------------------------------|
Szerezze meg, miután a kapcsolaton transzparens nem jeleníti meg a napló-parancssorban | Tekintse át ezt oldal: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez akkor fordulhat elő, ha futtatja egy egyéni virtuális Gépet, megerősített készülék vagy a rendszerindítási konfigurációs adott causers Windows megfelelően csatlakozni a soros port sikertelen lesz.
Írja be a SAC kérdezzen rá Ha engedélyezve van a kernel hibakeresés nem sikerült | A virtuális gép, és futtassa az RDP `bcdedit /debug {current} off` egy rendszergazda jogú parancssorból. Ha Ön nem használhatja az RDP lehet helyette az operációsrendszer-lemez csatolása egy másik Azure virtuális géphez és módosítani, amíg csatlakoztatva, egy lemezt a `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, majd felcserélheti a lemez vissza.
Ha az eredeti ismétlődő jellegű rendelkezett SAC eredményez olyan harmadik karaktert a PowerShell történő beillesztéskor | Egy megkerülő megoldás az elmúlt el a PSReadLine modul az aktuális munkamenet. Futtatás `Remove-Module PSReadLine` való eltávolítása a PSReadLine modul az aktuális munkamenet - ezzel nem törli vagy a modul eltávolítása.
Bizonyos billentyűzetet bemenetek állít elő kimenetet. SAC furcsa. (pl. `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) escape-karaktersorozatokat nem támogatja a SAC használatával.
Illessze be a nagyon hosszú karakterláncok nem működik | Soros konzol illeszthetők be a terminál 2048 karakter karakterláncok hossza korlátozza. Ez az a soros port sávszélesség túlterhelésének elkerülése érdekében.

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése egy problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként kevesebb (elsődleges) visszajelzés küldése a keresztül azserialhelp@microsoft.com vagy a virtuális gép kategóriáját http://feedback.azure.com

**Q. Támogatja a soros konzol másolja és illessze be?**

A. Igen hajtja végre. Ctrl + Shift + C és a Ctrl + Shift + V használatával másolja és illessze be a terminálba.

**Q. Ki engedélyezheti vagy letilthatja a soros konzol az előfizetésemet?**

A. Annak érdekében, hogy engedélyezi vagy letiltja a soros konzol egy előfizetési szintű szintjén, az előfizetés írási engedéllyel kell rendelkeznie. Írási engedéllyel rendelkező szerepek közé tartozik, de nem kizárólagosan, a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is rendelkezhetnek írási jogosultsággal.

**Q. Virtuális gép soros konzol férhet hozzá?**

A. Közreműködője szintű hozzáféréssel kell rendelkeznie legalább egy virtuális géphez a virtuális gép soros konzol eléréséhez. 

**Q. A soros konzol nem látható semmi, mi a teendő?**

A. A rendszerkép valószínűleg hibásan konfigurált, soros hozzáféréshez. Lásd: [engedélyezése soros konzolon, az egyéni vagy régebbi rendszerképek](#enable-serial-console-in-custom-or-older-images) engedélyezése a soros konzol lemezképeiben konfigurációval kapcsolatos részletekért.

**Q. Soros konzolon érhető el a Virtual Machine Scale Sets?**

A. A virtuális gép méretezési csoport példányaihoz a soros konzoljához való hozzáférés jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések
* A részletes útmutatót a cmd Parancsot, és a PowerShell parancsokat is használhat, a Windows SAC, kattintson a [Itt](serial-console-cmd-ps-commands.md).
* A soros konzolon érhető el is [Linux](serial-console-linux.md) virtuális gépeket.
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).