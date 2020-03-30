---
title: Az SSH-kapcsolattal kapcsolatos problémák elhárítása Azure virtuális géppel | Microsoft dokumentumok
description: Linuxot futtató Azure-virtuális gépek esetében az "SSH-kapcsolat sikertelen" vagy az "SSH-kapcsolat megtagadva" problémák elhárítása.
keywords: ssh kapcsolat elutasítva, ssh hiba, azure ssh, SSH kapcsolat nem sikerült
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060664"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Egy sikertelen, hibát eredményező vagy elutasított Azure-beli Linux rendszerű virtuális gép SSH-kapcsolatainak hibaelhárítása
Ez a cikk segít megtalálni és kijavítani a secure shell (SSH) hibák, SSH-kapcsolat hibák vagy SSH megtagadva, amikor megpróbál csatlakozni egy Linux virtuális gép (VM). Az Azure Portal, az Azure CLI vagy a VM Access Extension for Linux használatával elháríthatja és elháríthatja a csatlakozási problémákat.


Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.** Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Minden hibaelhárítási lépés után próbálja meg újra csatlakoztatni a virtuális géphez.

1. [Állítsa alaphelyzetbe az SSH konfigurációt](#reset-config).
2. [Állítsa alaphelyzetbe a](#reset-credentials) felhasználó hitelesítő adatait.
3. Ellenőrizze, hogy a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) szabályai engedélyezik-e az SSH-forgalmat.
   * Győződjön meg arról, hogy [létezik egy hálózati biztonsági csoport szabály](#security-rules) az SSH-forgalom engedélyezéséhez (alapértelmezés szerint 22-es TCP-port).
   * A portátirányítás /leképezés nem használható Azure-terheléselosztó használata nélkül.
4. Ellenőrizze a [virtuális gép erőforrás állapotát.](../../resource-health/resource-health-overview.md)
   * Győződjön meg arról, hogy a virtuális gép kifogástalan állapotúként jelent.
   * Ha [a rendszerindítási diagnosztika engedélyezve van,](boot-diagnostics.md)ellenőrizze, hogy a virtuális gép nem jelenti-e a rendszerindítási hibákat a naplókban.
5. [Indítsa újra a virtuális gép](#restart-vm).
6. [Telepítse újra a virtuális gép](#redeploy-vm).

Folytassa az olvasást a részletesebb hibaelhárítási lépésekért és magyarázatokért.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Az SSH-csatlakozási problémák elhárítására rendelkezésre álló módszerek
A hitelesítő adatok at vagy az SSH-konfigurációt az alábbi módszerek egyikével állíthatja alaphelyzetbe:

* [Azure Portal](#use-the-azure-portal) – nagyszerű, ha gyorsan alaphelyzetbe kell állítania az SSH-konfigurációt vagy az SSH-kulcsot, és nincs telepítve az Azure-eszközök.
* [Azure VM serial console](https://aka.ms/serialconsolelinux) – a virtuális gép soros konzolja az SSH-konfigurációtól függetlenül működik, és egy interaktív konzolt biztosít a virtuális gépszámára. Valójában a "nem SSH" helyzetek kifejezetten az, amit a soros konzol célja az volt, hogy segítsen megoldani. A részleteket alább találja.
* [Azure CLI](#use-the-azure-cli) – ha már a parancssorban van, gyorsan állítsa alaphelyzetbe az SSH-konfigurációt vagy hitelesítő adatokat. Ha klasszikus virtuális géptel dolgozik, használhatja az [Azure klasszikus CLI-t.](#use-the-azure-classic-cli)
* [Azure VMAccessForLinux-bővítmény](#use-the-vmaccess-extension) – hozzon létre és használja fel újra a json-definíciós fájlokat az SSH-konfiguráció vagy a felhasználói hitelesítő adatok alaphelyzetbe állításához.

Minden hibaelhárítási lépés után próbáljon meg újra csatlakozni a virtuális géphez. Ha továbbra sem tud csatlakozni, próbálkozzon a következő lépéssel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata
Az Azure Portal on egy gyors módja az SSH-konfiguráció vagy a felhasználói hitelesítő adatok alaphelyzetbe állítása anélkül, hogy bármilyen eszközt a helyi számítógépen.

Először válassza ki a virtuális gép az Azure Portalon. Görgessen le a **Támogatás + Hibaelhárítás** szakaszhoz, és válassza a **Jelszó alaphelyzetbe állítása lehetőséget** az alábbi példában:

![SSH-konfiguráció vagy hitelesítő adatok alaphelyzetbe állítása az Azure Portalon](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />Az SSH-konfiguráció alaphelyzetbe állítása
Az SSH-konfiguráció alaphelyzetbe állításához válassza `Reset configuration only` a **Mód** szakaszban az előző képernyőképen látható lehetőséget, majd kattintson a Frissítés **gombra.** Miután ez a művelet befejeződött, próbálja meg újra elérni a virtuális gép.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Egy felhasználó SSH hitelesítő adatainak visszaállítása
Egy meglévő felhasználó hitelesítő adatainak alaphelyzetbe `Reset password` állításához válassza a Mode **(Mód)** szakaszegyikét `Reset SSH public key` vagy elemét az előző képernyőképen látható módon. Adja meg a felhasználónevet és az SSH-kulcsot vagy az új jelszót, majd válassza **a Frissítés**lehetőséget.

Ebből a menüből sudo jogosultságokkal rendelkező felhasználót is létrehozhat a virtuális gépen. Adjon meg egy új felhasználónevet és a hozzájuk tartozó jelszót vagy SSH-kulcsot, majd válassza **a Frissítés**lehetőséget.

### <a name="check-security-rules"></a><a id="security-rules" />Biztonsági szabályok ellenőrzése

Az [IP-folyamat ellenőrzése](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) segítségével ellenőrizze, hogy egy hálózati biztonsági csoport egyik szabálya blokkolja-e a virtuális gépre irányuló vagy onnan érkező forgalmat. A hatályos biztonsági csoportszabályokat is áttekintheti annak érdekében, hogy a bejövő "Engedélyezés" NSG-szabály létezik, és az SSH-port (alapértelmezett 22) prioritása legyen. További információ: [Hatékony biztonsági szabályok használata a virtuális gépek forgalmának elhárításához.](../../virtual-network/diagnose-network-traffic-filter-problem.md)

### <a name="check-routing"></a>Útválasztás ellenőrzése

A Network Watcher [Következő ugrási](../../network-watcher/network-watcher-check-next-hop-portal.md) képességével ellenőrizze, hogy egy útvonal nem akadályozza-e meg a virtuális gépre vagy a virtuális gépről történő átirányítást. A hatékony útvonalak áttekintésével megtekintheti a hálózati adapter összes hatékony útvonalát is. További információ: [Hatékony útvonalak használata a virtuális gép forgalomforgalmának elhárításához.](../../virtual-network/diagnose-network-routing-problem.md)

## <a name="use-the-azure-vm-serial-console"></a>Az Azure VM soros konzoljának használata
Az [Azure VM serial console](./serial-console-linux.md) hozzáférést biztosít a szöveges alapú konzol Linux virtuális gépek. A konzol segítségével elháríthatja az SSH-kapcsolatot egy interaktív rendszerhéjban. Győződjön meg arról, hogy teljesítette a Soros konzol használatának [előfeltételeit,](./serial-console-linux.md#prerequisites) és próbálkozzon az alábbi parancsokkal az SSH-kapcsolat további hibaelhárításához.

### <a name="check-that-ssh-is-running"></a>Annak ellenőrzése, hogy fut-e az SSH
A következő paranccsal ellenőrizheti, hogy az SSH fut-e a virtuális gépen:

```console
ps -aux | grep ssh
```

Ha van kimenet, az SSH működik.

### <a name="check-which-port-ssh-is-running-on"></a>Annak ellenőrzése, hogy melyik porton fut az SSH

A következő paranccsal ellenőrizheti, hogy melyik porton fut az SSH:

```console
sudo grep Port /etc/ssh/sshd_config
```

A kimenet a következőre fog hasonlítani:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Ha még nem tette meg, telepítse a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és jelentkezzen be egy Azure-fiókba [az az login](/cli/azure/reference-index)használatával.

Ha létrehozott és feltöltött egy egyéni Linux-lemezképet, győződjön meg arról, hogy a [Microsoft Azure Linux Agent](../extensions/agent-linux.md) 2.0.5-ös vagy újabb verziója telepítve van. A Galéria lemezképeivel létrehozott virtuális gépek esetében ez a hozzáférési bővítmény már telepítve van és konfigurálva van.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció alaphelyzetbe állítása
Kezdetben megpróbálhatja alaphelyzetbe állítja az SSH-konfigurációt az alapértelmezett értékekre, és újraindíthatja az SSH-kiszolgálót a virtuális gépen. Ez nem módosítja a felhasználói fiók nevét, jelszavát vagy SSH-kulcsait.
A következő példa az [az vm-felhasználó reset-ssh](/cli/azure/vm/user) segítségével alaphelyzetbe `myResourceGroup`állítja az SSH konfigurációt a megnevezett `myVM` virtuális gépen. Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó SSH hitelesítő adatainak visszaállítása
A következő példa [az az vm felhasználói frissítés ével](/cli/azure/vm/user) alaphelyzetbe állítja a hitelesítő adatokat `myUsername` a megadott értékre `myPassword`a. `myVM` `myResourceGroup` Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

SSH-kulcshitelesítés használata esetén visszaállíthatja az SSH-kulcsot egy adott felhasználó számára. A következő példa az **az vm access set-linux-user** segítségével `~/.ssh/id_rsa.pub` frissíti a `myUsername`felhasználó neve ssh-kulcsát a programban megnevezett `myVM` virtuális gépen. `myResourceGroup` Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>A VMAccess-bővítmény használata
A VM Access Extension for Linux egy jsonfájlban olvasható, amely meghatározza a végrehajtandó műveleteket. Ezek a műveletek közé tartozik az SSHD alaphelyzetbe állítása, az SSH-kulcs alaphelyzetbe állítása vagy egy felhasználó hozzáadása. Továbbra is használja az Azure CLI a VMAccess-bővítmény hívásához, de szükség esetén újra felhasználhatja a jsonfájlokat több virtuális gépen. Ez a megközelítés lehetővé teszi, hogy hozzon létre egy tárházat a json fájlokat, amelyek ezután meghívható adott forgatókönyvek.

### <a name="reset-sshd"></a>SSHD visszaállítása
A következő `settings.json` tartalommal elnevezett fájl létrehozása:

```json
{
    "reset_ssh":"True"
}
```

Az Azure CLI használatával ezután hívja meg a bővítményt az `VMAccessForLinux` SSHD-kapcsolat alaphelyzetbe állításához a jsonfájl megadásával. A következő példa [az az vm bővítménykészletet](/cli/azure/vm/extension) használ `myVM` az `myResourceGroup`SSHD alaphelyzetbe állításához a megnevezett virtuális gépen. Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó SSH hitelesítő adatainak visszaállítása
Ha úgy tűnik, hogy az SSHD megfelelően működik, alaphelyzetbe állíthatja az adakozó felhasználó hitelesítő adatait. A felhasználó jelszavának alaphelyzetbe állításához `settings.json`hozzon létre egy nevű fájlt. A következő példa visszaállítja `myUsername` a hitelesítő adatokat a megadott `myPassword`értékre. Írja be a `settings.json` következő sorokat a fájlba a saját értékei vel:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Vagy a felhasználó SSH-kulcsának alaphelyzetbe állításához először hozzon létre egy `settings.json`. A következő példa visszaállítja `myUsername` a hitelesítő adatokat a megadott `myPassword` `myVM` értékre a. `myResourceGroup` Írja be a `settings.json` következő sorokat a fájlba a saját értékei vel:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

A jsonfájl létrehozása után az Azure CLI használatával hívja meg a bővítményt az `VMAccessForLinux` SSH felhasználói hitelesítő adatainak alaphelyzetbe állításához a jsonfájl megadásával. A következő példa visszaállítja a `myVM` hitelesítő `myResourceGroup`adatokat a megnevezett virtuális gép. Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Az Azure klasszikus CLI használata
Ha még nem tette meg, [telepítse az Azure klasszikus CLI-t, és csatlakozzon azure-előfizetéséhez.](../../cli-install-nodejs.md) Győződjön meg arról, hogy az Erőforrás-kezelő módot az alábbiak szerint használja:

```azurecli
azure config mode arm
```

Ha létrehozott és feltöltött egy egyéni Linux-lemezképet, győződjön meg arról, hogy a [Microsoft Azure Linux Agent](../extensions/agent-linux.md) 2.0.5-ös vagy újabb verziója telepítve van. A Galéria lemezképeivel létrehozott virtuális gépek esetében ez a hozzáférési bővítmény már telepítve van és konfigurálva van.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció alaphelyzetbe állítása
Lehet, hogy maga az SSHD-konfiguráció helytelenül van konfigurálva, vagy a szolgáltatás hibát észlelt. Az SSHD alaphelyzetbe állításával meggyőződhet arról, hogy maga az SSH-konfiguráció érvényes. Az SSHD alaphelyzetbe állítása az első hibaelhárítási lépés.

A következő példa visszaállítja az SSHD-t `myResourceGroup`egy, a nevű erőforráscsoportban megnevezett `myVM` virtuális gépen. A következő képpen használhatja saját virtuális gép- és erőforráscsoport-nevét:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó SSH hitelesítő adatainak visszaállítása
Ha úgy tűnik, hogy az SSHD megfelelően működik, visszaállíthatja az adakozó felhasználó jelszavát. A következő példa visszaállítja `myUsername` a hitelesítő adatokat a megadott `myPassword` `myVM` értékre a. `myResourceGroup` Használja a saját értékeit az alábbiak szerint:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

SSH-kulcshitelesítés használata esetén visszaállíthatja az SSH-kulcsot egy adott felhasználó számára. A következő példa frissíti a felhasználó `~/.ssh/id_rsa.pub` által tárolt `myUsername`SSH-kulcsot `myVM` a `myResourceGroup`megnevezett virtuális gépen. Használja a saját értékeit az alábbiak szerint:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Virtuális gép újraindítása
Ha alaphelyzetbe állította az SSH konfigurációját és a felhasználói hitelesítő adatokat, vagy hibát észlelt, megpróbálhatja újraindítani a virtuális gép újraindítását az alapul szolgáló számítási problémák megoldásához.

### <a name="azure-portal"></a>Azure portál
Ha újra szeretne indítani egy virtuális gép az Azure Portalon, válassza ki a virtuális gép, majd válassza **az Újraindítás,** mint az alábbi példában:

![Virtuális gép újraindítása az Azure Portalon](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
A következő példa az [az vm újraindítása](/cli/azure/vm) segítségével indítsa újra a névvel ellátott erőforráscsoportban megnevezett `myVM` virtuális gép. `myResourceGroup` Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A következő példa újraindítja `myVM` a névnek `myResourceGroup`nevezett erőforráscsoportban megnevezett virtuális gép. Használja a saját értékeit az alábbiak szerint:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Virtuális gép újratelepítése
Újratelepítheti a virtuális gép egy másik csomópont az Azure-ban, amely kijavíthatja az alapul szolgáló hálózati problémák. A virtuális gépek újratelepítéséről a [virtuális gép új Azure-csomópontra való újratelepítése](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben talál.

> [!NOTE]
> A művelet befejezése után az ideiglenes lemezadatok elvesznek, és a virtuális géphez társított dinamikus IP-címek frissülnek.
>
>

### <a name="azure-portal"></a>Azure portál
Virtuális gép újraüzembe helyezéséhez az Azure Portalon, válassza ki a virtuális gép, és görgessen le a **Támogatás + Hibaelhárítás** szakasz. Válassza az **Újratelepítés lehetőséget** az alábbi példában:

![Virtuális gép újratelepítése az Azure Portalon](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
A következő példa az [az vm újratelepítése](/cli/azure/vm) `myVM` használatával újratelepíteni `myResourceGroup`a virtuális gép neve s nevű erőforráscsoportban. Használja a saját értékeit az alábbiak szerint:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

A következő példa újratelepíti `myVM` a névvel `myResourceGroup`ellátott erőforráscsoportban megnevezett virtuális gép. Használja a saját értékeit az alábbiak szerint:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>A klasszikus telepítési modell használatával létrehozott virtuális gépek

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Próbálja meg ezeket a lépéseket a klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek leggyakoribb SSH-kapcsolati hibáinak megoldásához. Minden lépés után próbálja meg újra csatlakoztatni a virtuális géphez.

* Állítsa alaphelyzetbe a távelérést az [Azure Portalról.](https://portal.azure.com) Az Azure Portalon válassza ki a virtuális gép, majd válassza **a Távoli visszaállítása...** lehetőséget.
* Indítsa újra a virtuális gépet. Az [Azure Portalon](https://portal.azure.com)válassza ki a virtuális gép, és válassza **az Újraindítás**lehetőséget.

* Telepítse újra a virtuális gép egy új Azure-csomópontra. A virtuális gépek újratelepítéséről a [virtuális gép új Azure-csomópontra való újratelepítése](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben talál.

    A művelet befejezése után az ideiglenes lemezadatok elvesznek, és a virtuális géphez társított dinamikus IP-címek frissülnek.
* Kövesse a [Jelszó vagy a Linux-alapú virtuális gépek SSH-jának visszaállítására](../linux/classic/reset-access-classic.md) vonatkozó utasításokat a következő célokra:

  * Állítsa alaphelyzetbe a jelszót vagy az SSH-kulcsot.
  * Sudo *sudo* felhasználói fiók létrehozása.
  * Állítsa alaphelyzetbe az SSH konfigurációt.
* Ellenőrizze a virtuális gép erőforrás-állapotát a platformproblémák.<br>
     Válassza ki a virtuális gép, és görgessen le **beállítások** > **ellenőrzése állapot**.

## <a name="additional-resources"></a>További források
* Ha az alábbi lépések után továbbra sem tudja az SSH-t a virtuális géphez, tekintse meg a [további hibaelhárítási lépéseket](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a probléma megoldásához szükséges további lépések áttekintéséhez.
* Az alkalmazások hozzáférésének hibaelhárításáról az [Azure virtuális gépen futó alkalmazáshoz való hozzáférés hibaelhárítása című](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) témakörben talál további információt.
* A klasszikus telepítési modell használatával létrehozott virtuális gépek hibaelhárításáról a [Jelszó alaphelyzetbe állítása vagy a Linux-alapú virtuális gépek SSH-ja](../linux/classic/reset-access-classic.md)című témakörben talál további információt.
