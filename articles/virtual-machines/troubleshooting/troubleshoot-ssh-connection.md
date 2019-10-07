---
title: Az SSH-kapcsolatok problémáinak elhárítása egy Azure-beli virtuális gépen | Microsoft Docs
description: A Linux rendszerű Azure-beli virtuális gépeken az "SSH-kapcsolatok sikertelen" vagy "SSH-kapcsolatok elutasításával" kapcsolatos hibák elhárítása.
keywords: SSH-kapcsolatok elutasítása, SSH-hiba, Azure SSH, SSH-kapcsolatok nem sikerült
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 006dbbe1b7472982a894691d019eb88ef2041dac
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088262"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Az olyan Azure Linux rendszerű virtuális gépek SSH-kapcsolatainak hibaelhárítása, amelyek sikertelenek, hibák vagy elutasítottak
Ez a cikk segít megkeresni és kijavítani a Secure Shell-(SSH-) hibák, SSH-kapcsolódási hibák vagy SSH-problémák miatti problémákat, amikor megpróbál csatlakozni egy linuxos virtuális géphez (VM). A kapcsolódási problémák elhárításához és megoldásához használhatja a Linux Azure Portal, Azure CLI vagy VM-hozzáférési bővítményét.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géppel.

1. [Állítsa alaphelyzetbe az SSH-konfigurációt](#reset-config).
2. A felhasználó [hitelesítő adatainak alaphelyzetbe állítása](#reset-credentials) .
3. Ellenőrizze, hogy a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) szabályai engedélyezik-e az SSH-forgalmat.
   * Győződjön meg arról, hogy létezik egy [hálózati biztonsági csoport szabálya](#security-rules) az SSH-forgalom engedélyezéséhez (alapértelmezés szerint a 22-es TCP-port).
   * A port átirányítása/leképezése nem használható Azure Load Balancer használata nélkül.
4. Keresse meg a [virtuális gép erőforrás-állapotát](../../resource-health/resource-health-overview.md).
   * Győződjön meg arról, hogy a virtuális gép állapota Kifogástalan.
   * Ha engedélyezve van a [rendszerindítási diagnosztika](boot-diagnostics.md), ellenőrizze, hogy a virtuális gép nem jelent-e rendszerindítási hibákat a naplókban.
5. [Indítsa újra a virtuális gépet](#restart-vm).
6. [Telepítse újra a virtuális gépet](#redeploy-vm).

Folytassa az olvasást részletesebb hibaelhárítási lépésekkel és magyarázatokkal.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Elérhető módszerek az SSH-kapcsolódási problémák elhárításához
A hitelesítő adatokat vagy SSH-konfigurációt az alábbi módszerek egyikével állíthatja alaphelyzetbe:

* [Azure Portal](#use-the-azure-portal) – nagyszerű, ha gyorsan alaphelyzetbe kell ÁLLÍTANIA az SSH-konfigurációt vagy az SSH-kulcsot, és nincs telepítve az Azure-eszközök.
* [Azure VM soros konzol](https://aka.ms/serialconsolelinux) – a virtuális gép soros konzolja az SSH-konfigurációtól függetlenül fog működni, és egy interaktív konzolt biztosít a virtuális géphez. Valójában a "nem SSH" helyzetek kifejezetten a soros konzol megoldásához lettek tervezve. Részletek alább.
* [Azure CLI](#use-the-azure-cli) – ha már a parancssorban van, gyorsan visszaállíthatja az SSH-konfigurációt vagy a hitelesítő adatokat. Ha klasszikus virtuális géppel dolgozik, használhatja a [klasszikus Azure CLI](#use-the-azure-classic-cli)-t.
* [Azure VMAccessForLinux bővítmény](#use-the-vmaccess-extension) – JSON-definíciós fájlok létrehozása és újbóli felhasználása az SSH-konfiguráció vagy a felhasználó hitelesítő adatainak alaphelyzetbe állításához.

Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géphez való csatlakozással. Ha továbbra sem tud kapcsolatot létesíteni, próbálkozzon a következő lépéssel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal segítségével gyorsan alaphelyzetbe állíthatja az SSH-konfigurációt vagy a felhasználói hitelesítő adatokat anélkül, hogy az eszközöket telepítené a helyi számítógépen.

A kezdéshez válassza ki a virtuális gépet a Azure Portalban. Görgessen le a **támogatás + hibaelhárítás** szakaszhoz, és válassza a **jelszó alaphelyzetbe állítása** lehetőséget az alábbi példában látható módon:

![Az SSH-konfiguráció vagy a hitelesítő adatok alaphelyzetbe állítása a Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a>@no__t – az SSH-konfiguráció 0Reset
Az SSH-konfiguráció alaphelyzetbe állításához válassza a `Reset configuration only` elemet a **Mode** szakaszban az előző képernyőképen, majd válassza a **frissítés**lehetőséget. Ha a művelet befejeződött, próbálja meg újra elérni a virtuális gépet.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Reset SSH hitelesítő adatai a felhasználó számára
Egy meglévő felhasználó hitelesítő adatainak alaphelyzetbe állításához válassza ki a `Reset SSH public key` vagy a `Reset password` értéket a **mód** szakaszban, ahogy az előző képernyőképen. Adja meg a felhasználónevet és az SSH-kulcsot vagy az új jelszót, majd válassza a **frissítés**lehetőséget.

Ezen a menüben létrehozhat egy sudo jogosultságokkal rendelkező felhasználót is a virtuális gépen. Adja meg az új felhasználónevet és a hozzá tartozó jelszót vagy SSH-kulcsot, majd válassza a **frissítés**lehetőséget.

### <a name="a-idsecurity-rules-check-security-rules"></a>@no__t – 0Check biztonsági szabályok

Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) ellenőrizze, hogy egy hálózati biztonsági csoportban lévő szabály blokkolja-e a virtuális gép felé irányuló vagy onnan érkező forgalmat. A hatályos biztonsági csoportok szabályait is áttekintheti, így biztosítva, hogy a bejövő "engedélyezés" NSG szabály létezik, és az SSH-portra van rangsorolva (alapértelmezés szerint 22). További információ: [hatékony biztonsági szabályok használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Útválasztás keresése

A [következő ugrási](../../network-watcher/network-watcher-check-next-hop-portal.md) lehetőséggel ellenőrizheti, hogy egy adott útvonal nem akadályozza-e a forgalmat a virtuális gépekről vagy rendszerről. Network Watcher Az érvényes útvonalakat is áttekintheti a hálózati adapterek összes érvényes útvonalának megtekintéséhez. További információ: [hatékony útvonalak használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Az Azure-beli virtuális gép soros konzoljának használata
Az [Azure VM soros konzol](./serial-console-linux.md) hozzáférést biztosít a Linux rendszerű virtuális gépekhez készült szöveges konzolhoz. A konzol segítségével az SSH-kapcsolatokat egy interaktív rendszerhéjban lehet elhárítani. Győződjön meg arról, hogy teljesítette a soros konzol használatának [előfeltételeit](./serial-console-linux.md#prerequisites) , és próbálja ki az alábbi PARANCSOKAT az SSH-kapcsolat további hibakereséséhez.

### <a name="check-that-ssh-is-running"></a>Győződjön meg arról, hogy az SSH fut
A következő parancs használatával ellenőrizheti, hogy az SSH fut-e a virtuális gépen:
```
$ ps -aux | grep ssh
```
Ha bármilyen kimenet van, az SSH működik.

### <a name="check-which-port-ssh-is-running-on"></a>Győződjön meg arról, hogy az SSH melyik porton fut
A következő parancs használatával ellenőrizhető, hogy az SSH melyik porton fut:
```
$ sudo grep Port /etc/ssh/sshd_config
```
A kimenet az alábbihoz hasonló lesz:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Ha még nem tette meg, telepítse a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Ha létrehozta és feltöltött egy egyéni linuxos lemezképet, győződjön meg arról, hogy a [Microsoft Azure Linux-ügynök](../extensions/agent-linux.md) 2.0.5 vagy újabb verziója telepítve van. A katalógus-lemezképek használatával létrehozott virtuális gépek esetén ez a hozzáférési kiterjesztés már telepítve van és konfigurálva van.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció visszaállítása
Először próbálja meg alapértékre állítani az SSH-konfigurációt, és indítsa újra az SSH-kiszolgálót a virtuális gépen. Ez nem változtatja meg a felhasználói fiók nevét, jelszavát vagy SSH-kulcsát.
Az alábbi példa az [az VM User reset-SSH](/cli/azure/vm/user) használatával állítja vissza az SSH-konfigurációt a `myVM` nevű virtuális gépen a `myResourceGroup`-ben. A saját értékeit a következőképpen használhatja:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Felhasználó SSH hitelesítő adatainak alaphelyzetbe állítása
Az alábbi példa az [az VM User Update paranccsal](/cli/azure/vm/user) állítja vissza a `myUsername` hitelesítő adatait a `myPassword` mezőben megadott értékre a (z) `myVM` nevű virtuális gépen `myResourceGroup`-ben. A saját értékeit a következőképpen használhatja:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Ha SSH-kulcsos hitelesítést használ, alaphelyzetbe állíthatja egy adott felhasználó SSH-kulcsát. Az alábbi példa az **az VM Access set-Linux-User** paranccsal frissíti a `~/.ssh/id_rsa.pub` nevű SSH-kulcsot a (z) `myUsername` nevű felhasználó számára a `myVM` nevű virtuális gépen @no__t – 4. A saját értékeit a következőképpen használhatja:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>A VMAccess bővítmény használata
A Linux rendszerhez készült virtuálisgép-hozzáférési bővítmény egy olyan JSON-fájlban olvas be, amely meghatározza a végrehajtandó műveleteket. Ezek a műveletek közé tartozik az SSHD alaphelyzetbe állítása, az SSH-kulcs alaphelyzetbe állítása vagy a felhasználó hozzáadása. Továbbra is használhatja az Azure CLI-t a VMAccess-bővítmény meghívásához, de szükség esetén újra felhasználhatja a JSON-fájlokat több virtuális gépen. Ez a módszer lehetővé teszi a JSON-fájlok tárházának létrehozását, amely az adott forgatókönyvekhez hívható.

### <a name="reset-sshd"></a>Az SSHD alaphelyzetbe állítása
Hozzon létre egy `settings.json` nevű fájlt a következő tartalommal:

```json
{
    "reset_ssh":"True"
}
```

Az Azure CLI használatával a JSON-fájl megadásával meghívja a `VMAccessForLinux` kiterjesztést az SSHD-kapcsolatok visszaállításához. Az alábbi példa az [az VM Extension set](/cli/azure/vm/extension) paranccsal állítja alaphelyzetbe az sshd-t a `myVM` nevű virtuális gépen @no__t – 2. A saját értékeit a következőképpen használhatja:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Felhasználó SSH hitelesítő adatainak alaphelyzetbe állítása
Ha az SSHD úgy tűnik, hogy megfelelően működjön, alaphelyzetbe állíthatja egy adakozó felhasználó hitelesítő adatait. Egy felhasználó jelszavának alaphelyzetbe állításához hozzon létre egy `settings.json` nevű fájlt. A következő példa visszaállítja a `myUsername` hitelesítő adatait a `myPassword` értékben megadott értékre. Adja meg a következő sorokat a `settings.json` fájlban a saját értékek használatával:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Vagy egy felhasználó SSH-kulcsának alaphelyzetbe állításához először hozzon létre egy `settings.json` nevű fájlt. A következő példa visszaállítja a `myUsername` hitelesítő adatait a (z) `myPassword`, `myVM` nevű virtuális gépen `myResourceGroup` értékben megadott értékre. Adja meg a következő sorokat a `settings.json` fájlban a saját értékek használatával:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

A JSON-fájl létrehozása után az Azure CLI-vel hívhatja meg a `VMAccessForLinux` kiterjesztést az SSH-felhasználói hitelesítő adatok alaphelyzetbe állításához a JSON-fájl megadásával. A következő példa visszaállítja a hitelesítő adatokat a `myVM` nevű virtuális gépen `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>A klasszikus Azure parancssori felület használata
Ha még nem tette meg, [telepítse a klasszikus Azure CLI-t, és kapcsolódjon az Azure-előfizetéséhez](../../cli-install-nodejs.md). Győződjön meg arról, hogy Resource Manager-módot használ a következőképpen:

```azurecli
azure config mode arm
```

Ha létrehozta és feltöltött egy egyéni linuxos lemezképet, győződjön meg arról, hogy a [Microsoft Azure Linux-ügynök](../extensions/agent-linux.md) 2.0.5 vagy újabb verziója telepítve van. A katalógus-lemezképek használatával létrehozott virtuális gépek esetén ez a hozzáférési kiterjesztés már telepítve van és konfigurálva van.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció visszaállítása
Előfordulhat, hogy az SSHD-konfiguráció hibásan van konfigurálva, vagy a szolgáltatás hibát észlelt. Alaphelyzetbe állíthatja az SSHD-t, hogy az SSH-konfiguráció érvényes legyen. Az SSHD alaphelyzetbe állításához az első hibaelhárítási lépésnek kell lennie.

Az alábbi példa alaphelyzetbe állítja az SSHD-t egy `myVM` nevű virtuális gépen `myResourceGroup` nevű erőforráscsoporthoz. A következő módon használhatja saját virtuálisgép-és erőforráscsoport-nevét:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Felhasználó SSH hitelesítő adatainak alaphelyzetbe állítása
Ha az SSHD úgy tűnik, hogy megfelelően működjön, alaphelyzetbe állíthatja egy adakozó felhasználó jelszavát. A következő példa visszaállítja a `myUsername` hitelesítő adatait a (z) `myPassword`, `myVM` nevű virtuális gépen `myResourceGroup` értékben megadott értékre. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Ha SSH-kulcsos hitelesítést használ, alaphelyzetbe állíthatja egy adott felhasználó SSH-kulcsát. Az alábbi példa frissíti az `~/.ssh/id_rsa.pub` nevű SSH-kulcsot a (z) `myUsername` nevű felhasználóhoz a (z) `myVM` nevű virtuális gépen `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a>@no__t – virtuális gép 0Restart
Ha alaphelyzetbe állítja az SSH-konfigurációt és a felhasználói hitelesítő adatokat, vagy hibát észlelt, akkor próbálja meg újraindítani a virtuális gépet az alapul szolgáló számítási problémák megoldásához.

### <a name="azure-portal"></a>Azure Portal
Ha a Azure Portal használatával szeretne újraindítani egy virtuális gépet, válassza ki a virtuális gépet, majd válassza az **Újraindítás** lehetőséget az alábbi példában látható módon:

![Virtuális gép újraindítása a Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Az alábbi példa az [az VM újraindítását](/cli/azure/vm) használja a `myVM` nevű virtuális gép újraindításához a `myResourceGroup` nevű erőforráscsoporthoz. A saját értékeit a következőképpen használhatja:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
A következő példa újraindítja a `myVM` nevű virtuális gépet az `myResourceGroup` nevű erőforráscsoport-csoportban. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a>@no__t – virtuális gép 0Redeploy
Az Azure-on belül újratelepítheti a virtuális gépet egy másik csomópontra, amely az összes mögöttes hálózati problémát kijavítani tudja. A virtuális gépek újratelepítésével kapcsolatos további információkért lásd: [virtuális gép újratelepítése új Azure-csomópontra](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> A művelet befejezése után a rendszer elveszíti az ideiglenes lemezeket, és frissíti a virtuális géppel társított dinamikus IP-címeket.
>
>

### <a name="azure-portal"></a>Azure Portal
Ha a Azure Portal használatával szeretné újratelepíteni a virtuális gépet, válassza ki a virtuális gépet, és görgessen le a **támogatás + hibaelhárítás** szakaszhoz. Válassza az újbóli **üzembe helyezés** lehetőséget az alábbi példában látható módon:

![Virtuális gép újbóli üzembe helyezése a Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
A következő példa az [az VM redeploy](/cli/azure/vm) paranccsal helyezi üzembe a `myVM` nevű virtuális gépet az `myResourceGroup` nevű erőforráscsoporthoz. A saját értékeit a következőképpen használhatja:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
A következő példa újratelepíti a `myVM` nevű virtuális gépet az `myResourceGroup` nevű erőforráscsoport-csoportba. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>A klasszikus üzemi modell használatával létrehozott virtuális gépek
Próbálja ki ezeket a lépéseket a klasszikus üzemi modell használatával létrehozott virtuális gépek leggyakoribb SSH-kapcsolódási hibáinak megoldásához. Az egyes lépések után próbálkozzon újra a virtuális géppel.

* A [Azure Portal](https://portal.azure.com)távoli elérésének visszaállítása. A Azure Portal válassza ki a virtuális gépet, majd válassza a **távoli alaphelyzetbe állítás...** lehetőséget.
* Indítsa újra a virtuális gépet. A [Azure Portal](https://portal.azure.com)válassza ki a virtuális gépet, és válassza az **Újraindítás**lehetőséget.

* Telepítse újra a virtuális gépet egy új Azure-csomópontra. További információ a virtuális gépek újbóli üzembe helyezéséről: [virtuális gép újratelepítése új Azure-csomópontra](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    A művelet befejezése után a rendszer elveszíti az ideiglenes lemezeket, és frissíti a virtuális géppel társított dinamikus IP-címeket.
* Kövesse a [jelszó vagy SSH Linux-alapú virtuális gépekre való alaphelyzetbe állításának](../linux/classic/reset-access-classic.md) lépéseit a következő témakörben:

  * Állítsa vissza a jelszót vagy az SSH-kulcsot.
  * Hozzon létre egy *sudo* felhasználói fiókot.
  * Állítsa alaphelyzetbe az SSH-konfigurációt.
* Keresse meg a virtuális gép erőforrás-állapotát bármilyen platformra vonatkozó probléma esetén.<br>
     Válassza ki a virtuális gépet, és görgessen le a **beállítások**@no__t – 1**állapotot**.

## <a name="additional-resources"></a>További források
* Ha továbbra sem tud SSH-t létesíteni a virtuális géppel a következő lépések után, tekintse meg a [részletes hibaelhárítási lépéseket](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a probléma megoldásához szükséges további lépések áttekintéséhez.
* Az alkalmazás-hozzáférés hibaelhárításával kapcsolatos további információkért lásd: Azure-beli [virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* A klasszikus üzemi modellel létrehozott virtuális gépek hibaelhárításával kapcsolatos további információkért lásd: [jelszó vagy SSH alaphelyzetbe állítása Linux-alapú virtuális gépekhez](../linux/classic/reset-access-classic.md).
