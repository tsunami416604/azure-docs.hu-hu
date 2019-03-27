---
title: Az Azure virtuális gépek soros konzolja Linux rendszeren |} A Microsoft Docs
description: Azure-beli virtuális gépek soros konzol és a kétirányú.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: f407d87249c44ad3a4773b2cd8fc85ee09506ceb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445660"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Linuxos virtuális gépek soros konzolja

Az Azure Portalon a virtuális gép (VM) soros konzol Linux rendszerű virtuális gépek egy szöveges alapú konzol hozzáférést biztosít. A soros kapcsolat a virtuális gép, azt a virtuális gép hálózati vagy az operációs rendszer állapotának független való hozzáférés biztosítása a COM1 soros porton csatlakozik. Virtuális gép a soros konzoljához való hozzáférés csak az Azure portal használatával hajtható végre. Ez csak a virtuális géphez a virtuális gépek Közreműködője vagy magasabb hozzáférési szerepkörrel rendelkező felhasználók engedélyezett.

Windows virtuális gépek soros konzol dokumentációjáért lásd: [a Windows virtuális gépek soros konzolja](../windows/serial-console.md).

> [!NOTE]
> Globális Azure-régiókban található virtuális gépek a soros konzol szolgáltatás általánosan elérhető. Ez még nem érhető el az Azure government vagy Azure China felhőkben.


## <a name="prerequisites"></a>Előfeltételek

- A virtuális gép, amelyben a soros konzol éri el a resource management üzemi modellhez kell használnia. Klasszikus üzemi modellben nem támogatottak.

- Rendelkeznie kell a virtuális gép, amelyben éri el a soros konzol [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van.

    ![Rendszerindítási diagnosztikai beállításokat](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- A soros konzol használó fióknak rendelkeznie kell a [virtuális gépek Közreműködője szerepkör](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiók:

- A virtuális gép, amelyben éri el a soros konzol jelszóalapú fiókkal kell rendelkeznie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény funkcióját. Válassza ki **jelszó alaphelyzetbe állítása** származó a **támogatás + hibaelhárítás** szakaszban.

- Linux-disztribúciók jellemző beállításait, lásd: [soros konzol Linux terjesztési rendelkezésre állási](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>A soros konzol használatának első lépései
A soros konzol virtuális gépek csak az Azure Portalon keresztül érhető el:

  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. A bal oldali menüben válassza ki a **virtuális gépek**.

  1. Válassza ki egy virtuális Gépet a listából. A virtuális gép megnyílik.

  1. Görgessen le a **támogatás + hibaelhárítás** szakaszt, és válassza **soros konzol**. A soros konzol segítségével egy új panel nyílik meg, és elindítja a kapcsolatot.

     ![Soros konzol ablakából Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE]
> A soros konzol beállított jelszóval rendelkező helyi felhasználó szükséges. Az SSH nyilvános kulcs csak a konfigurált virtuális gépeket nem lehet bejelentkezni a soros konzol. Egy helyi felhasználót egy jelszó létrehozásához használja a [VMAccess bővítmény](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), amely érhető el a portálon kiválasztásával **jelszó alaphelyzetbe állítása** az Azure Portalon, és hozzon létre egy helyi felhasználói jelszó.
> A rendszergazdai jelszó alaphelyzetbe állítása szerint a fiókban [GRUB használatával egyfelhasználós módban indul](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Soros konzol Linux terjesztési rendelkezésre állása
A soros konzol megfelelő működéséhez konfigurálni kell a vendég operációs rendszer olvasása és írása az üzenetek konzol a soros port. A legtöbb [Azure által támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) a soros konzol alapértelmezés szerint konfigurálva van. Kiválasztásával **soros konzol** a a **támogatás + hibaelhárítás** az Azure Portalon szakasza nyújt a soros konzoljához való hozzáférés.

Disztribúció      | Soros hozzáférés
:-----------|:---------------------
Red Hat Enterprise Linux    | Soros hozzáférés alapértelmezés szerint engedélyezve van.
CentOS      | Soros hozzáférés alapértelmezés szerint engedélyezve van.
Ubuntu      | Soros hozzáférés alapértelmezés szerint engedélyezve van.
CoreOS      | Soros hozzáférés alapértelmezés szerint engedélyezve van.
SUSE        | Az Azure-ban elérhető újabb SLES lemezképek hozzáférése soros konzolon alapértelmezés szerint engedélyezve van. Ha régebbi verzióit (10 vagy korábbi) SLES használja az Azure-on, tekintse meg a [tudásbáziscikk](https://www.novell.com/support/kb/doc.php?id=3456486) soros konzol engedélyezéséhez.
Oracle Linux        | Soros hozzáférés alapértelmezés szerint engedélyezve van.
Egyéni Linux-rendszerképek     | A Linux rendszerű virtuális gép egyéni rendszerkép soros konzolon engedélyezéséhez a fájl hozzáféréséhez a konzolhoz */etc/inittab* , futtassa a parancsot egy terminálban `ttyS0`. Például: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Megfelelően az egyéni lemezképek létrehozásával kapcsolatos további információkért lásd: [létrehozása és feltöltése az Azure-ban Linux rendszerű virtuális merevlemez](https://aka.ms/createuploadvhd). Ha egy egyéni kernel fejleszt, érdemes megfontolni a kernel jelző: `CONFIG_SERIAL_8250=y` és `CONFIG_MAGIC_SYSRQ_SERIAL=y`. A konfigurációs fájl helye általában a */boot/* elérési útja.

> [!NOTE]
> Ha semmit a soros konzol nem láthatóak, ügyeljen arra, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális Gépen.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>A soros konzol eléréséhez a gyakori forgatókönyvek

Forgatókönyv          | A soros konzol műveletek
:------------------|:-----------------------------------------
Hibás *FSTAB* fájl | Nyomja le az **Enter** billentyűt a folytatáshoz, és a egy szövegszerkesztő segítségével hárítsa el a *FSTAB* fájlt. Előfordulhat, hogy kell lenniük ehhez egyfelhasználós módban. További információkért lásd: a soros konzol szakaszában [fstab-hibák megoldása](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) és [GRUB és egyfelhasználós üzemmódban használja a soros konzol](serial-console-grub-single-user-mode.md).
Helytelen tűzfal-szabályok |  Engedélyezze az iptables SSH-kapcsolat blokkolására van beállítva, ha a soros konzol segítségével anélkül, hogy az SSH a virtuális gép kezelése. További részleteket tekinthet meg a [engedélyezze az iptables ember lap](https://linux.die.net/man/8/iptables). Ehhez hasonlóan ha Ön firewalld SSH-hozzáférését blokkolja, hozzáférhet a virtuális gép soros konzolon keresztül és firewalld újrakonfigurálása. További részletek találhatók a [firewalld dokumentáció](https://firewalld.org/documentation/).
Fájlrendszer sérülése és ellenőrzése | Tekintse át a soros konzol szakaszában [Azure Linux rendszerű virtuális gép nem indítható el miatt fájlrendszer hibáinak](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) további hibaelhárítási részleteket sérült fájlrendszerek soros konzol használatával.
SSH konfigurációs problémák | A soros konzol eléréséhez, és módosítsa a beállításokat. Soros konzol használható függetlenül a virtuális gépek SSH-konfigurációja nem követeli meg a virtuális gép hálózati kapcsolat működjön. Hibaelhárítási útmutató érhető el: [hibaelhárítása SSH kapcsolatok az Azure Linux rendszerű virtuális géphez, amelyek nem sikerül, hibák, vagy elutasítják](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). További részletek [részletes SSH hibaelhárítási lépései Linux rendszerű virtuális gép az Azure-ban való csatlakozással kapcsolatos problémákat](./detailed-troubleshoot-ssh-connection.md)
A rendszertöltő használata | Indítsa újra a virtuális Gépet a soros konzol panelen a Linux rendszerű virtuális gépen lévő GRUB eléréséhez. További részletek és disztribúció kapcsolatos információkat lásd: [GRUB és egyfelhasználós üzemmódban használja a soros konzol](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Tiltsa le a soros konzol
Alapértelmezés szerint minden előfizetés rendelkezik a soros konzol hozzáférés engedélyezve van az összes virtuális gép. A soros konzol vagy az előfizetés szintjén, vagy a virtuális gép szintjén is letilthatja.

> [!NOTE]
> Engedélyezi vagy letiltja a soros konzol-előfizetéssel, az előfizetés írási engedélyekkel rendelkeznie. Ezeket az engedélyeket a rendszergazda vagy tulajdonos szerepkörök közé tartozik. Egyéni szerepkörök is lehet írási engedéllyel.

### <a name="subscription-level-disable"></a>Előfizetés-szintű letiltása
A soros konzolon keresztül egy teljes előfizetésre letiltható a [tiltsa le a konzolon REST API-hívás](/rest/api/serialconsole/console/disableconsole). Használhatja a **Kipróbálom** funkció letiltása és engedélyezése a soros konzol egy előfizetés az API dokumentációja oldalon érhető el. Adja meg az előfizetés-azonosítója **subscriptionId**, adja meg **alapértelmezett** a **alapértelmezett**, majd válassza ki **futtatása**. Az Azure CLI-parancsok még nem érhetők el.

![REST API-t próbálja ki](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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

> [!CAUTION]
> A konzol nem hozzáférést jelszavát a rendszer naplózza. Azonban a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket, vagy bármely más személyes azonosításra alkalmas adatok (PII) kimeneti, ha azok lesz írva az a virtuális gép rendszerindítási diagnosztikai naplók. Ezek lesz írva az összes többi látható szöveg, valamint a soros konzolon görgessen vissza végrehajtásának részeként függvény. Ezeket a naplókat. kör alakú és egyetlen egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező tudja elérni őket. Azonban javasoljuk, hogy kövesse az ajánlott eljárás szerint az összes adat távoli asztallal, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok.

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó megszakítja, és a második felhasználó csatlakozik-e ugyanabban a munkamenetben.

> [!CAUTION]
> Ez azt jelenti, hogy le van választva a felhasználó nem kijelentkeztetjük. Képes érvényesíteni a kijelentkezési kapcsolat bontása esetén (használatával SIGHUP vagy hasonló mechanizmus) továbbra is szerepel az ütemterv részét képezi. A Windows nincs engedélyezve a speciális felügyeleti konzol (SAC); egy automatikus időkorlátja Linux esetén konfigurálhatja a terminál időkorlátozási beállítást. Ehhez adja hozzá a `export TMOUT=600` a a *.bash_profile* vagy *.profile* fájlt a felhasználó jelentkezzen be a konzol használatával. Ez a beállítás a munkamenet 10 perc elteltével időtúllépést okoz.

## <a name="accessibility"></a>Kisegítő lehetőségek
Kisegítő lehetőségek egy kulcsfontosságú fókuszának az Azure-soros konzolon. Ebből a célból keresztülhaladnak, hogy a soros konzolon érhető el teljesen.

### <a name="keyboard-navigation"></a>Billentyűzetnavigációt
Használja a **lapon** billentyűt a billentyűzeten a soros konzol felületen az Azure Portalról nyissa meg. A hely kiemelten jelenik meg a képernyőn. Hagyja a lépéseknek az ismertetése, a soros konzol ablakából, nyomja le a **Ctrl**+**F6** a billentyűzeten.

### <a name="use-the-serial-console-with-a-screen-reader"></a>A soros konzol használata a képernyőolvasó
A soros konzol rendelkezik beépített képernyőolvasó támogatása. Kapcsolva képernyőolvasóval való navigálni lehetővé teszi a helyettesítő szöveget a jelenleg kijelölt gombra kattintva a képernyőolvasó felolvassa olvasni.

## <a name="errors"></a>Hibák
Mivel a legtöbb hiba átmeneti, a kapcsolat újrapróbálása milyen gyakran oldhatja meg őket. Az alábbi táblázat azon hibákat és kezelési lehetőségeiket listáját.

Hiba                            |   Kezelés
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait  *&lt;VMNAME&gt;*. A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van.
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. | A virtuális gép a soros konzol eléréséhez elindított állapotban kell lennie.
Nem rendelkezik a szükséges engedélyekkel, ez a virtuális gép használata a soros konzol. Győződjön meg arról, hogy a virtuális gépek Közreműködője szerepkör engedélyei.| A soros konzol hozzáférést bizonyos engedélyekre van szüksége. További információkért lásd: [Előfeltételek](#prerequisites).
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának  *&lt;STORAGEACCOUNTNAME&gt;*. Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | A soros konzol hozzáférést bizonyos engedélyekre van szüksége. További információkért lásd: [Előfeltételek](#prerequisites).
Web socket le van zárva, vagy nem nyitható meg. | Szükség lehet az engedélyezési listára `*.console.azure.com`. Egy részletesebb, de hosszabb megközelítés az engedélyezési listára a [a Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653), amelyek viszonylag sűrűn változnak.
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nincs egy fiók tűzfal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.

## <a name="known-issues"></a>Ismert problémák
A soros konzol problémák tisztában vagyunk. Ezekről a problémákról és kockázatcsökkentési lépések listáját itt látható.

Probléma                           |   Kezelés
:---------------------------------|:--------------------------------------------|
Billentyű **Enter** után a kapcsolaton transzparens, nem váltják ki a bejelentkezési kérések jelenhetnek megjeleníteni. | További információkért lásd: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). A probléma akkor fordulhat elő, ha egy egyéni virtuális Gépet, megerősített készülék vagy megfelelően csatlakozni a soros port sikertelen Linux okozó GRUB konfigurációs futtatja.
Soros konzol szöveg mindössze egy részét a képernyő méretétől (gyakran után egy szövegszerkesztő használatával). | Soros konzol nem támogatják a vonatkozó ablakméret egyeztetése ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), ez azt jelenti, nem lesznek nincs SIGWINCH jel küldött frissíteni a képernyőméret és a virtuális gép nincs tudomásuk a terminálban mérete fog rendelkezni. Telepítse a xterm vagy egy hasonló segédprogramot, és adja meg a `resize` parancsot, és futtassa `resize`.
Illessze be a hosszú karakterláncok nem működik. | A soros konzol illeszthetők be a terminál 2048 karakter hosszúságú lehet, megelőzve a soros port sávszélesség sztring hossza korlátozza.
Soros konzol nem működik egy storage-fiók tűzfal. | Soros konzol szándékosan nem képes együttműködni az engedélyezve a rendszerindítás-diagnosztikai tárfiók a storage-fiók tűzfalak.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés a githubon található létrehozásával https://aka.ms/serialconsolefeedback. Másik lehetőségként (kevésbé elsődleges), visszajelzést küldhet keresztül azserialhelp@microsoft.com vagy virtuálisgép-kategóriába https://feedback.azure.com.

**Q. Támogatja a soros konzol másolja és illessze be?**

A. Igen. Használat **Ctrl**+**Shift**+**C** és **Ctrl**+**Shift** + **V** másolja és illessze be a terminálba.

**Q. Használható a soros konzol helyett az SSH-kapcsolatot?**

A. Tűnhet, hogy a használat technikailag lehetséges, amíg a soros konzol használható elsősorban a hibaelhárító eszköz helyzetekben, ahol nem lehetséges SSH-n keresztüli kapcsolat célja. Azt javasoljuk, szemben a soros konzol segítségével egy SSH-helyettesítője a következő okok miatt:

- A soros konzol nem rendelkezik a legtöbb sávszélesség SSH-t. Mivel a szolgáltatás csak szöveg kapcsolat, több grafikus felhasználói Felülettel műveltekből interakciók nehezen.
- Soros hozzáférés rendszer a pillanatnyilag lehetségesnél csak a felhasználónév és jelszó használatával. Mivel az SSH-kulcsok sokkal biztonságosabb, mint felhasználónév/jelszó kombináció, biztonsági szempontból ajánlott SSH soros konzolon keresztül.

**Q. Ki engedélyezheti vagy letilthatja a soros konzol az előfizetésemet?**

A. Engedélyezi vagy letiltja a soros konzol egy előfizetési szintű szintjén, az előfizetés írási engedélyekkel rendelkeznie. Írási engedéllyel rendelkező szerepek közé tartozik a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is lehet írási engedéllyel.

**Q. Virtuális gép férhet hozzá a soros konzol?**

A. A virtuális gép közreműködő szerepkörrel kell rendelkeznie legalább egy virtuális géphez a virtuális gép soros konzol eléréséhez.

**Q. A soros konzol nem jelennek meg, mi a teendő?**

A. A rendszerkép valószínűleg hibásan konfigurált, soros hozzáféréshez. A lemezkép engedélyezése a soros konzol konfigurálásával kapcsolatos további információkért lásd: [soros konzol Linux terjesztési rendelkezésre állási](#serial-console-linux-distribution-availability).

**Q. A soros konzolon érhető el a virtuálisgép-méretezési csoportok?**

A. Jelenleg a virtuális gép méretezési csoport példányaihoz a soros konzoljához való hozzáférés nem támogatott.

**Q. Ha megadom a virtuális Gépet is csak SSH-kulcs hitelesítés használatával, akkor is használhatom a soros konzol saját virtuális Géphez való csatlakozáshoz?**

A. Igen. A soros konzol nincs szükség az SSH-kulcsokat, mert csak állítsa be a felhasználónév/jelszó kombináció kell. Kiválasztásával teheti **jelszó alaphelyzetbe állítása** az Azure Portalon, és ezekkel a hitelesítő adatokkal jelentkezzen be a soros konzol használatával.

## <a name="next-steps"></a>További lépések
* Használja a soros konzol [GRUB és egyfelhasználós üzemmódban](serial-console-grub-single-user-mode.md).
* A soros konzol a [NMI és SysRq hívások](serial-console-nmi-sysrq.md).
* Ismerje meg, hogyan használható a soros konzol [GRUB engedélyezése a különböző disztribúciók](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* A soros konzolon érhető el is [Windows virtuális gépek](../windows/serial-console.md).
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).

