---
title: Az SSH-kapcsolatok problémáinak elhárítása egy Azure-beli virtuális gépen | Microsoft Docs
description: A Linux rendszerű Azure-beli virtuális gépeken az "SSH-kapcsolatok sikertelen" vagy "SSH-kapcsolatok elutasításával" kapcsolatos hibák elhárítása.
keywords: SSH-kapcsolatok elutasítása, SSH-hiba, Azure SSH, SSH-kapcsolatok nem sikerült
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
ms.openlocfilehash: c0f4e02a76044268946a4a482eaeccf5d622b8a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036264"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Egy sikertelen, hibát eredményező vagy elutasított Azure-beli Linux rendszerű virtuális gép SSH-kapcsolatainak hibaelhárítása
Ez a cikk segít megkeresni és kijavítani a Secure Shell-(SSH-) hibák, SSH-kapcsolódási hibák vagy SSH-problémák miatti problémákat, amikor megpróbál csatlakozni egy linuxos virtuális géphez (VM). A kapcsolódási problémák elhárításához és megoldásához használhatja a Linux Azure Portal, Azure CLI vagy VM-hozzáférési bővítményét.


Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géppel.

1. [Állítsa alaphelyzetbe az SSH-konfigurációt](#reset-config).
2. A felhasználó [hitelesítő adatainak alaphelyzetbe állítása](#reset-credentials) .
3. Ellenőrizze, hogy a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) szabályai engedélyezik-e az SSH-forgalmat.
   * Győződjön meg arról, hogy létezik egy [hálózati biztonsági csoport szabálya](#security-rules) az SSH-forgalom engedélyezéséhez (alapértelmezés szerint a 22-es TCP-port).
   * A port átirányítása/leképezése nem használható Azure Load Balancer használata nélkül.
4. Keresse meg a [virtuális gép erőforrás-állapotát](../../service-health/resource-health-overview.md).
   * Győződjön meg arról, hogy a virtuális gép állapota Kifogástalan.
   * Ha engedélyezve van a [rendszerindítási diagnosztika](boot-diagnostics.md), ellenőrizze, hogy a virtuális gép nem jelent-e rendszerindítási hibákat a naplókban.
5. [Indítsa újra a virtuális gépet](#restart-vm).
6. [Telepítse újra a virtuális gépet](#redeploy-vm).

Folytassa az olvasást részletesebb hibaelhárítási lépésekkel és magyarázatokkal.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Elérhető módszerek az SSH-kapcsolódási problémák elhárításához
A hitelesítő adatokat vagy SSH-konfigurációt az alábbi módszerek egyikével állíthatja alaphelyzetbe:

* [Azure Portal](#use-the-azure-portal) – nagyszerű, ha gyorsan alaphelyzetbe kell ÁLLÍTANIA az SSH-konfigurációt vagy az SSH-kulcsot, és nincs telepítve az Azure-eszközök.
* [Azure VM soros konzol](https://aka.ms/serialconsolelinux) – a virtuális gép soros konzolja az SSH-konfigurációtól függetlenül fog működni, és egy interaktív konzolt biztosít a virtuális géphez. Valójában a "nem SSH" helyzetek kifejezetten a soros konzol megoldásához lettek tervezve. A részleteket alább találja.
* [Azure CLI](#use-the-azure-cli) – ha már a parancssorban van, gyorsan visszaállíthatja az SSH-konfigurációt vagy a hitelesítő adatokat. Ha klasszikus virtuális géppel dolgozik, használhatja a [klasszikus Azure CLI](#use-the-azure-classic-cli)-t.
* [Azure VMAccessForLinux bővítmény](#use-the-vmaccess-extension) – JSON-definíciós fájlok létrehozása és újbóli felhasználása az SSH-konfiguráció vagy a felhasználó hitelesítő adatainak alaphelyzetbe állításához.

Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géphez való csatlakozással. Ha továbbra sem tud kapcsolatot létesíteni, próbálkozzon a következő lépéssel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal segítségével gyorsan alaphelyzetbe állíthatja az SSH-konfigurációt vagy a felhasználói hitelesítő adatokat anélkül, hogy az eszközöket telepítené a helyi számítógépen.

A kezdéshez válassza ki a virtuális gépet a Azure Portalban. Görgessen le a **támogatás + hibaelhárítás** szakaszhoz, és válassza a **jelszó alaphelyzetbe állítása** lehetőséget az alábbi példában látható módon:

![Az SSH-konfiguráció vagy a hitelesítő adatok alaphelyzetbe állítása a Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />Az SSH-konfiguráció alaphelyzetbe állítása
Az SSH-konfiguráció alaphelyzetbe állításához válassza a `Reset configuration only` **Mode (mód** ) szakaszt az előző képernyőképen, majd válassza a **frissítés**lehetőséget. Ha a művelet befejeződött, próbálja meg újra elérni a virtuális gépet.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Egy felhasználó SSH hitelesítő adatainak visszaállítása
Egy meglévő felhasználó hitelesítő adatainak alaphelyzetbe állításához válassza `Reset SSH public key` `Reset password` az előző képernyőképen, vagy a **mód** szakaszban. Adja meg a felhasználónevet és az SSH-kulcsot vagy az új jelszót, majd válassza a **frissítés**lehetőséget.

Ezen a menüben létrehozhat egy sudo jogosultságokkal rendelkező felhasználót is a virtuális gépen. Adja meg az új felhasználónevet és a hozzá tartozó jelszót vagy SSH-kulcsot, majd válassza a **frissítés**lehetőséget.

### <a name="check-security-rules"></a><a id="security-rules" />Biztonsági szabályok keresése

Az [IP-folyamat ellenőrzésével](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) ellenőrizze, hogy egy hálózati biztonsági csoportban lévő szabály blokkolja-e a virtuális gép felé irányuló vagy onnan érkező forgalmat. A hatályos biztonsági csoportok szabályait is áttekintheti, így biztosítva, hogy a bejövő "engedélyezés" NSG szabály létezik, és az SSH-portra van rangsorolva (alapértelmezés szerint 22). További információ: [hatékony biztonsági szabályok használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Útválasztás keresése

A [következő ugrási](../../network-watcher/diagnose-vm-network-routing-problem.md) lehetőséggel ellenőrizheti, hogy egy adott útvonal nem akadályozza-e a forgalmat a virtuális gépekről vagy rendszerről. Network Watcher Az érvényes útvonalakat is áttekintheti a hálózati adapterek összes érvényes útvonalának megtekintéséhez. További információ: [hatékony útvonalak használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Az Azure-beli virtuális gép soros konzoljának használata
Az [Azure VM soros konzol](./serial-console-linux.md) hozzáférést biztosít a Linux rendszerű virtuális gépekhez készült szöveges konzolhoz. A konzol segítségével az SSH-kapcsolatokat egy interaktív rendszerhéjban lehet elhárítani. Győződjön meg arról, hogy teljesítette a soros konzol használatának [előfeltételeit](./serial-console-linux.md#prerequisites) , és próbálja ki az alábbi PARANCSOKAT az SSH-kapcsolat további hibakereséséhez.

### <a name="check-that-ssh-is-running"></a>Győződjön meg arról, hogy az SSH fut
A következő parancs használatával ellenőrizheti, hogy az SSH fut-e a virtuális gépen:

```console
ps -aux | grep ssh
```

Ha bármilyen kimenet van, az SSH működik.

### <a name="check-which-port-ssh-is-running-on"></a>Győződjön meg arról, hogy az SSH melyik porton fut

A következő parancs használatával ellenőrizhető, hogy az SSH melyik porton fut:

```console
sudo grep Port /etc/ssh/sshd_config
```

A kimenet az alábbihoz hasonló lesz:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Ha még nem tette meg, telepítse a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Ha létrehozta és feltöltött egy egyéni linuxos lemezképet, győződjön meg arról, hogy a [Microsoft Azure Linux-ügynök](../extensions/agent-linux.md) 2.0.5 vagy újabb verziója telepítve van. A katalógus-lemezképek használatával létrehozott virtuális gépek esetén ez a hozzáférési kiterjesztés már telepítve van és konfigurálva van.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció visszaállítása
Először próbálja meg alapértékre állítani az SSH-konfigurációt, és indítsa újra az SSH-kiszolgálót a virtuális gépen. Ez nem változtatja meg a felhasználói fiók nevét, jelszavát vagy SSH-kulcsát.
Az alábbi példa az az [VM User reset-SSH](/cli/azure/vm/user) használatával állítja vissza az SSH-konfigurációt a (z) nevű virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó SSH hitelesítő adatainak visszaállítása
Az alábbi példa az az [VM User Update paranccsal](/cli/azure/vm/user) állítja vissza a hitelesítő adatokat a (z `myUsername` ) rendszerben megadott értékre a (z `myPassword` ) nevű virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Ha SSH-kulcsos hitelesítést használ, alaphelyzetbe állíthatja egy adott felhasználó SSH-kulcsát. Az alábbi példa az az **VM Access set-Linux-User** paranccsal frissíti a nevű felhasználónál tárolt SSH-kulcsot a nevű `~/.ssh/id_rsa.pub` `myUsername` virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>A VMAccess bővítmény használata
A Linux rendszerhez készült virtuálisgép-hozzáférési bővítmény egy olyan JSON-fájlban olvas be, amely meghatározza a végrehajtandó műveleteket. Ezek a műveletek közé tartozik az SSHD alaphelyzetbe állítása, az SSH-kulcs alaphelyzetbe állítása vagy a felhasználó hozzáadása. Továbbra is használhatja az Azure CLI-t a VMAccess-bővítmény meghívásához, de szükség esetén újra felhasználhatja a JSON-fájlokat több virtuális gépen. Ez a módszer lehetővé teszi a JSON-fájlok tárházának létrehozását, amely az adott forgatókönyvekhez hívható.

### <a name="reset-sshd"></a>Az SSHD alaphelyzetbe állítása
Hozzon létre egy nevű fájlt `settings.json` a következő tartalommal:

```json
{
    "reset_ssh":True
}
```

Az Azure CLI használatával a `VMAccessForLinux` JSON-fájl megadásával meghívja a bővítményt, hogy alaphelyzetbe állítsa az sshd-kapcsolatokat. Az alábbi példa az [az VM Extension set](/cli/azure/vm/extension) paranccsal állítja vissza az sshd-t a (z) nevű virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó SSH hitelesítő adatainak visszaállítása
Ha az SSHD úgy tűnik, hogy megfelelően működjön, alaphelyzetbe állíthatja egy adakozó felhasználó hitelesítő adatait. Egy felhasználó jelszavának alaphelyzetbe állításához hozzon létre egy nevű fájlt `settings.json` . A következő példa visszaállítja a hitelesítő adatokat a `myUsername` alkalmazásban megadott értékre `myPassword` . Adja meg a következő sorokat a `settings.json` fájlba a saját értékeinek használatával:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Vagy egy felhasználó SSH-kulcsának alaphelyzetbe állításához először hozzon létre egy nevű fájlt `settings.json` . Az alábbi példa alaphelyzetbe állítja a hitelesítő adatokat a alkalmazásban `myUsername` megadott értékre a (z `myPassword` ) nevű virtuális gépen `myVM` `myResourceGroup` . Adja meg a következő sorokat a `settings.json` fájlba a saját értékeinek használatával:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

A JSON-fájl létrehozása után az Azure CLI használatával hívja meg a `VMAccessForLinux` bővítményt az SSH-felhasználói hitelesítő adatok alaphelyzetbe állításához a JSON-fájl megadásával. Az alábbi példa alaphelyzetbe állítja a hitelesítő adatokat a (z) nevű virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>A klasszikus Azure parancssori felület használata
Ha még nem tette meg, [telepítse a klasszikus Azure CLI-t, és kapcsolódjon az Azure-előfizetéséhez](/cli/azure/install-classic-cli). Győződjön meg arról, hogy Resource Manager-módot használ a következőképpen:

```azurecli
azure config mode arm
```

Ha létrehozta és feltöltött egy egyéni linuxos lemezképet, győződjön meg arról, hogy a [Microsoft Azure Linux-ügynök](../extensions/agent-linux.md) 2.0.5 vagy újabb verziója telepítve van. A katalógus-lemezképek használatával létrehozott virtuális gépek esetén ez a hozzáférési kiterjesztés már telepítve van és konfigurálva van.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció visszaállítása
Előfordulhat, hogy az SSHD-konfiguráció hibásan van konfigurálva, vagy a szolgáltatás hibát észlelt. Alaphelyzetbe állíthatja az SSHD-t, hogy az SSH-konfiguráció érvényes legyen. Az SSHD alaphelyzetbe állításához az első hibaelhárítási lépésnek kell lennie.

A következő példa alaphelyzetbe állítja az SSHD-t a nevű erőforráscsoport egyik virtuális gépén `myVM` `myResourceGroup` . A következő módon használhatja saját virtuálisgép-és erőforráscsoport-nevét:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó SSH hitelesítő adatainak visszaállítása
Ha az SSHD úgy tűnik, hogy megfelelően működjön, alaphelyzetbe állíthatja egy adakozó felhasználó jelszavát. Az alábbi példa alaphelyzetbe állítja a hitelesítő adatokat a alkalmazásban `myUsername` megadott értékre a (z `myPassword` ) nevű virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Ha SSH-kulcsos hitelesítést használ, alaphelyzetbe állíthatja egy adott felhasználó SSH-kulcsát. Az alábbi példa frissíti a nevű felhasználóhoz tartozó SSH-kulcsot a nevű `~/.ssh/id_rsa.pub` `myUsername` virtuális gépen `myVM` `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Virtuális gép újraindítása
Ha alaphelyzetbe állítja az SSH-konfigurációt és a felhasználói hitelesítő adatokat, vagy hibát észlelt, akkor próbálja meg újraindítani a virtuális gépet az alapul szolgáló számítási problémák megoldásához.

### <a name="azure-portal"></a>Azure Portal
Ha a Azure Portal használatával szeretne újraindítani egy virtuális gépet, válassza ki a virtuális gépet, majd válassza az **Újraindítás** lehetőséget az alábbi példában látható módon:

![Virtuális gép újraindítása a Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Az alábbi példa az az [VM restart](/cli/azure/vm) paranccsal indítja újra a `myVM` nevű virtuális gépet a named Resource csoportban `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A következő példa újraindítja a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Virtuális gép ismételt üzembe helyezése
Az Azure-on belül újratelepítheti a virtuális gépet egy másik csomópontra, amely az összes mögöttes hálózati problémát kijavítani tudja. A virtuális gépek újratelepítésével kapcsolatos további információkért lásd: [virtuális gép újratelepítése új Azure-csomópontra](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> A művelet befejezése után a rendszer elveszíti az ideiglenes lemezeket, és frissíti a virtuális géppel társított dinamikus IP-címeket.
>
>

### <a name="azure-portal"></a>Azure Portal
Ha a Azure Portal használatával szeretné újratelepíteni a virtuális gépet, válassza ki a virtuális gépet, és görgessen le a **támogatás + hibaelhárítás** szakaszhoz. Válassza az újbóli **üzembe helyezés** lehetőséget az alábbi példában látható módon:

![Virtuális gép újbóli üzembe helyezése a Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Az alábbi példa az az [VM redeploy](/cli/azure/vm) paranccsal helyezi üzembe a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

A következő példa újból üzembe helyezi a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` . A saját értékeit a következőképpen használhatja:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>A klasszikus üzemi modell használatával létrehozott virtuális gépek

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Próbálja ki ezeket a lépéseket a klasszikus üzemi modell használatával létrehozott virtuális gépek leggyakoribb SSH-kapcsolódási hibáinak megoldásához. Az egyes lépések után próbálkozzon újra a virtuális géppel.

* A [Azure Portal](https://portal.azure.com)távoli elérésének visszaállítása. A Azure Portal válassza ki a virtuális gépet, majd válassza a **távoli alaphelyzetbe állítás...** lehetőséget.
* Indítsa újra a virtuális gépet. A [Azure Portal](https://portal.azure.com)válassza ki a virtuális gépet, és válassza az **Újraindítás**lehetőséget.

* Telepítse újra a virtuális gépet egy új Azure-csomópontra. További információ a virtuális gépek újbóli üzembe helyezéséről: [virtuális gép újratelepítése új Azure-csomópontra](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    A művelet befejezése után a rendszer elveszíti az ideiglenes lemezeket, és frissíti a virtuális géppel társított dinamikus IP-címeket.
* Kövesse a [jelszó vagy SSH Linux-alapú virtuális gépekre való alaphelyzetbe állításának](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) lépéseit a következő témakörben:

  * Állítsa vissza a jelszót vagy az SSH-kulcsot.
  * Hozzon létre egy *sudo* felhasználói fiókot.
  * Állítsa alaphelyzetbe az SSH-konfigurációt.
* Keresse meg a virtuális gép erőforrás-állapotát bármilyen platformra vonatkozó probléma esetén.<br>
     Válassza ki a virtuális gépet, és görgessen le a **Beállítások**  >  **állapotának**megtekintése elemre.

## <a name="additional-resources"></a>További források
* Ha továbbra sem tud SSH-t létesíteni a virtuális géppel a következő lépések után, tekintse meg a [részletes hibaelhárítási lépéseket](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a probléma megoldásához szükséges további lépések áttekintéséhez.
* Az alkalmazás-hozzáférés hibaelhárításával kapcsolatos további információkért lásd: Azure-beli [virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
* A klasszikus üzemi modellel létrehozott virtuális gépek hibaelhárításával kapcsolatos további információkért lásd: [jelszó vagy SSH alaphelyzetbe állítása Linux-alapú virtuális gépekhez](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic).
