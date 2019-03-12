---
title: Egy Azure virtuális géphez SSH-kapcsolatok problémáinak hibaelhárítása |} A Microsoft Docs
description: 'Hogyan háríthatók el a problémákat, például az "SSH-kapcsolat sikertelen" vagy "az SSH-kapcsolat elutasítva: Linux rendszerű Azure virtuális gép számára.'
keywords: ssh kapcsolat elutasítva, ssh hiba, az azure ssh, SSH-kapcsolatot nem sikerült
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 1c28c0bb3fdc2bb94595910ccff9f86769b17da5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547129"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Az Azure Linux VM, amely nem sikerül, hibák, vagy elutasítják az SSH-kapcsolatok hibaelhárítása
Ez a cikk segítségével megkeresheti és kijavíthatja a Secure Shell (SSH) hibák, az SSH-kapcsolati hibák, miatt előforduló problémákat, vagy az SSH elutasítják, amikor megpróbál kapcsolódni egy Linux rendszerű virtuális géphez (VM). Az Azure Portalon, az Azure CLI vagy a Linux VM-hozzáférési bővítmény használatával hibaelhárításához és kapcsolati problémák megoldásához.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás**. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépéseket
Hibaelhárítási lépések, után próbáljon újra csatlakozni a virtuális Gépet.

1. [Az SSH-konfiguráció visszaállítása](#reset-config).
2. [A hitelesítő adatok alaphelyzetbe állítása](#reset-credentials) a felhasználó számára.
3. Ellenőrizze a [hálózati biztonsági csoport](../../virtual-network/security-overview.md) szabályok lehetővé teszik az SSH-forgalmat.
   * Ügyeljen arra, hogy egy [hálózati biztonsági csoport szabálya](#security-rules) létezik-e az SSH-forgalom engedélyezése (alapértelmezés szerint 22-es TCP-port).
   * Nem használhat port átirányítás / leképezése az Azure load balancer használata nélkül.
4. Ellenőrizze a [virtuális gép a resource health](../../resource-health/resource-health-overview.md). 
   * Győződjön meg arról, hogy a virtuális gép jelenti, hogy kifogástalan-e.
   * Ha rendelkezik [rendszerindítási diagnosztika engedélyezve](boot-diagnostics.md), ellenőrizze, hogy a virtuális gép nem jelent meg a rendszerindítási hibák a naplókban.
5. [Indítsa újra a virtuális gép](#restart-vm).
6. [A virtuális gép ismételt üzembe](#redeploy-vm).

Részletesebb hibaelhárítási lépéseket és magyarázatok olvassa tovább.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Módszerek az SSH-kapcsolatok problémáinak hibaelhárítása
Új hitelesítő adatokat, vagy az SSH-konfigurációt az alábbi módszerek egyikével:

* [Az Azure portal](#use-the-azure-portal) – nagyszerű, ha gyorsan alaphelyzetbe az SSH-konfiguráció vagy az SSH-kulcsot kell, és nem rendelkezik Azure-eszközök telepítve.
* [Az Azure CLI](#use-the-azure-cli) – Ha már a parancssorban, gyorsan alaphelyzetbe az SSH-konfiguráció vagy a hitelesítő adatokat. Ha a klasszikus virtuális gép dolgozik, használhatja a [Azure klasszikus parancssori felület](#use-the-azure-classic-cli).
* [Az Azure a VMAccessForLinux bővítmény](#use-the-vmaccess-extension) – hozzon létre, és újra felhasználhatja a json-definíciós fájlokat, és az SSH konfigurációs vagy a felhasználó hitelesítő adatainak alaphelyzetbe állítása.

Hibaelhárítási lépések, után próbáljon csatlakozni a virtuális gép újra. Ha még nem sikerül, próbálja meg a következő lépéssel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata
Az Azure Portalon alaphelyzetbe az SSH konfigurációs vagy felhasználói hitelesítő adatok nélkül bármely eszközök telepítése a helyi számítógépen gyors lehetőséget kínál.

Első lépésként válassza ki a virtuális gép az Azure Portalon. Görgessen le a **támogatás + hibaelhárítás** szakaszt, és válassza **jelszó alaphelyzetbe állítása** az alábbi példában látható módon:

![Új SSH-konfiguráció vagy a hitelesítő adatokat az Azure Portalon](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />Az SSH-konfiguráció visszaállítása
Alaphelyzetbe az SSH-konfigurációt, válassza ki a `Reset configuration only` a a **mód** szakaszban az előző képernyőképen látható módon, majd válassza a **frissítés**. Ez a művelet befejezése után próbálja meg újból elérhető a virtuális gép.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Alaphelyzetbe az SSH-felhasználó hitelesítő adatai
Válassza egy meglévő felhasználó hitelesítő adatainak alaphelyzetbe állításához `Reset SSH public key` vagy `Reset password` a a **mód** szakaszban az előző képernyőképen látható módon. Adja meg a felhasználónevet és egy SSH-kulcs vagy új jelszót, majd válassza ki **frissítés**.

Egy felhasználót sudo jogosultsági szinttel a virtuális gép ebből a menüből is létrehozhat. Adjon meg egy új felhasználónevet és a kapcsolódó jelszó vagy SSH-kulcsot, és válassza ki **frissítés**.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />Ellenőrizze a biztonsági szabályok

Használat [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) , győződjön meg arról, ha egy szabály a hálózati biztonsági csoport blokkolja a bejövő és kimenő forgalmat a virtuális gépről. Emellett áttekintheti az érvényben lévő biztonsági csoport szabályai annak biztosítása érdekében a bejövő "Engedélyezés" NSG-t a szabály létezik, és van priorizálva (alapértelmezés: 22) SSH-port. További információkért lásd: [érvényes biztonsági szabályok használata a virtuális gépek forgalom áramlása](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Ellenőrizze az útválasztást

Network Watcher használatát [a következő Ugrás](../../network-watcher/network-watcher-check-next-hop-portal.md) teszi, hogy erősítse meg, hogy egy útvonal forgalom nem megakadályozza, hogy vagy a virtuális gépről. Érvényes útvonalak a hálózati adapter érvényes útvonalai tekintse át is. További információkért lásd: [érvényes útvonalak használata virtuális gépek forgalom áramlása](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Ha még nem tette, telepítse a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) , és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index).

Ha elkészült, és egyéni Linux lemezképét feltöltve, ellenőrizze, hogy a [a Microsoft Azure Linux-ügynök](../extensions/agent-windows.md) 2.0.5 verzió vagy újabb verziója szükséges. Image z Galerie használatával létrehozott virtuális gép esetében a hozzáférési bővítmény már telepített és konfigurált Önnek.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció visszaállítása
Először is próbálja meg az SSH-konfigurációjának visszaállítása az alapértelmezett értékeket, és az SSH-kiszolgálót a virtuális gép újraindítása. Ez nem változik a felhasználói fiók nevét, jelszó vagy SSH-kulcsokat.
Az alábbi példában [az virtuális gép felhasználói reset-ssh](/cli/azure/vm/user) nevű virtuális gépre SSH-konfiguráció alaphelyzetbe `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Alaphelyzetbe az SSH-felhasználó hitelesítő adatai
Az alábbi példában [az vm user frissítése](/cli/azure/vm/user) hitelesítő adatainak alaphelyzetbe állítása `myUsername` a megadott értékkel `myPassword`, nevű virtuális gépre `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Ha SSH-hitelesítést használ, alaphelyzetbe állíthatja az SSH-kulcsot az adott felhasználó számára. Az alábbi példában **az virtuális gép hozzáférés beállítása – linux-felhasználói** frissíteni az SSH-kulcs tárolható `~/.ssh/id_rsa.pub` nevű felhasználó `myUsername`, nevű virtuális gépre `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>A VMAccess bővítmény használata
A linuxos Virtuálisgép-hozzáférési bővítmény beolvassa egy json-fájlban, amely meghatározza a műveletek elvégzésére. Ezek a műveletek közé tartozik az SSHD alaphelyzetbe állítása, egy SSH-kulcsának visszaállítása vagy a felhasználó hozzáadása. Továbbra is az Azure CLI használatával a VMAccess bővítmény hívni, de újból felhasználhatja a json-fájlok több virtuális gép között szükség esetén. Ez a megközelítés lehetővé teszi, hogy hozzon létre egy json-fájlok, amelyek ezután meg lehet hívni a megadott forgatókönyvek tárház.

### <a name="reset-sshd"></a>Reset SSHD
Hozzon létre egy fájlt `settings.json` az alábbi tartalommal:

```json
{
    "reset_ssh":"True"
}
```

Az Azure CLI használatával, majd hívja a `VMAccessForLinux` bővítmény kapcsolat alaphelyzetbe állítása SSHD adjon meg egy json-fájlt. Az alábbi példában [az virtuálisgép-bővítmény csoportot](/cli/azure/vm/extension) alaphelyzetbe állítani a virtuális gép neve SSHD `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Alaphelyzetbe az SSH-felhasználó hitelesítő adatai
Ha SSHD jelenik meg a megfelelő működéshez, visszaállíthatja az előállító felhasználó hitelesítő adatait. A felhasználó jelszavának alaphelyzetbe állítása, hozzon létre egy fájlt `settings.json`. A következő példa alaphelyzetbe állítja a hitelesítő adatok `myUsername` a megadott értékkel `myPassword`. Adja meg a következő sorokat a `settings.json` fájlt, a saját értékeit használja:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Egy felhasználó az SSH-kulcs alaphelyzetbe állításához először hozzon létre egy fájlt vagy `settings.json`. A következő példa alaphelyzetbe állítja a hitelesítő adatok `myUsername` a megadott értékkel `myPassword`, nevű virtuális gépre `myVM` a `myResourceGroup`. Adja meg a következő sorokat a `settings.json` fájlt, a saját értékeit használja:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Miután létrehozta a json-fájlt, az Azure CLI használatával hívja a `VMAccessForLinux` bővítmény használatával alaphelyzetbe az SSH-felhasználói hitelesítő adatait adja meg a json-fájlt. A következő példa alaphelyzetbe állítja a virtuális gép nevű hitelesítő adatok `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>A klasszikus Azure parancssori felületének használata
Ha még nem tette, [a klasszikus Azure CLI telepítése és csatlakozás az Azure-előfizetéshez](../../cli-install-nodejs.md). Győződjön meg arról, hogy a Resource Manager módot a következő:

```azurecli
azure config mode arm
```

Ha elkészült, és egyéni Linux lemezképét feltöltve, ellenőrizze, hogy a [a Microsoft Azure Linux-ügynök](../extensions/agent-windows.md) 2.0.5 verzió vagy újabb verziója szükséges. Image z Galerie használatával létrehozott virtuális gép esetében a hozzáférési bővítmény már telepített és konfigurált Önnek.

### <a name="reset-ssh-configuration"></a>SSH-konfiguráció visszaállítása
A saját maga SSHD konfiguráció nincs megfelelően konfigurálva, vagy a szolgáltatás hibába ütközött. Ellenőrizze, hogy maga az SSH-konfigurációja érvényes SSHD visszaállíthatja. Alaphelyzetbe állítás SSHD kell lennie, igénybe vehet hibaelhárítás első lépése.

A következő példa alaphelyzetbe állítja a virtuális gép neve SSHD `myVM` az erőforráscsoport neve `myResourceGroup`. Használja a saját virtuális gép és az erőforráscsoport nevét a következőképpen:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Alaphelyzetbe az SSH-felhasználó hitelesítő adatai
Ha SSHD jelenik meg a megfelelő működéshez, visszaállíthatja az előállító felhasználó jelszavát. A következő példa alaphelyzetbe állítja a hitelesítő adatok `myUsername` a megadott értékkel `myPassword`, nevű virtuális gépre `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Ha SSH-hitelesítést használ, alaphelyzetbe állíthatja az SSH-kulcsot az adott felhasználó számára. Az alábbi példa frissíti az SSH-kulcs tárolható `~/.ssh/id_rsa.pub` nevű felhasználó `myUsername`, nevű virtuális gépre `myVM` a `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />Virtuális gép újraindítása
Ha az SSH konfigurációs és a felhasználói hitelesítő adatok alaphelyzetbe állítása, vagy ennek során hibába ütközött, megpróbálhatja újraindítani a mögöttes számítási problémák címet a virtuális Gépet.

### <a name="azure-portal"></a>Azure Portal
Indítsa újra a virtuális gép az Azure Portalon, válassza ki a virtuális Gépet, majd **indítsa újra a** az alábbi példában látható módon:

![Az Azure Portalon a virtuális gép újraindítása](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Az alábbi példában [az virtuális gép újraindítása](/cli/azure/vm) nevű virtuális gép újraindítását `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
A következő példa újraindítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />A virtuális gép ismételt üzembe
Egy virtuális Gépet egy másik csomópontra az Azure-ban, amely előfordulhat, hogy javítsa az alapul szolgáló hálózati problémákat is telepítse újra. További információ a virtuális gép újbóli: [újratelepíteni a virtuális gépet az új Azure csomópontra](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Ez a művelet befejezését követően elveszik az ideiglenes lemez adatait, és dinamikus IP-címek a virtuális géphez társított frissülnek.
> 
> 

### <a name="azure-portal"></a>Azure Portal
Ismételt üzembe helyezése egy virtuális Gépet az Azure Portalon, válassza ki a virtuális Gépet, és görgessen le a **támogatás + hibaelhárítás** szakaszban. Válassza ki **ismételt üzembe helyezése** az alábbi példában látható módon:

![Ismételt üzembe helyezése egy virtuális Gépet az Azure Portalon](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
A következő példa használatát [az vm redeploy](/cli/azure/vm) nevű virtuális gép ismételt üzembe `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
Az alábbi példa ismét üzembe helyezi a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit használja a következőképpen:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>A klasszikus üzemi modellel létrehozott virtuális gépek
Próbálja ki ezeket a lépéseket a klasszikus üzemi modell használatával létrehozott virtuális gépek számára a leggyakrabban használt SSH-kapcsolati hibák megoldásához. Minden lépése után próbáljon újra csatlakozni a virtuális Gépet.

* A távelérés alaphelyzetbe állítása a [az Azure portal](https://portal.azure.com). Az Azure Portalon, válassza ki a virtuális Gépet, majd **távoli alaphelyzetbe állítása...** .
* Indítsa újra a virtuális gépet. Az a [az Azure portal](https://portal.azure.com), válassza ki a virtuális Gépet, és válassza ki **indítsa újra a**.
    
* Telepítse újra a virtuális Gépet egy új Azure csomópontra. A virtuális gép ismételt üzembe kapcsolatos információkért lásd: [újratelepíteni a virtuális gépet az új Azure csomópontra](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Ez a művelet befejezése után a rövid élettartamú lemezt adatok elvesznek, és a dinamikus IP-címek a virtuális géphez társított frissülni fog.
* Kövesse a [alaphelyzetbe állítása a jelszó vagy SSH a Linux-alapú virtuális gépek](../linux/classic/reset-access-classic.md) való:
  
  * A jelszó vagy SSH-kulcs alaphelyzetbe.
  * Hozzon létre egy *sudo* felhasználói fiókot.
  * Állítsa vissza az SSH-konfigurációt.
* Ellenőrizze a virtuális gép a resource health tartozó platform problémákat.<br>
     Válassza ki a virtuális Gépet, és görgessen lefelé **beállítások** > **állapotának ellenőrzése**.

## <a name="additional-resources"></a>További források
* Ha SSH még mindig nem lehet kapcsolódni a virtuális Géphez utáni lépések végrehajtása után, tekintse meg [részletes hibaelhárítási lépések](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , tekintse át a további lépéseket a probléma megoldása érdekében.
* Alkalmazás-hozzáférés hibaelhárítással kapcsolatos további információkért lásd: [egy Azure virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* A klasszikus üzemi modellel létrehozott virtuális gépek hibaelhárítással kapcsolatos további információkért lásd: [alaphelyzetbe állítása a jelszó vagy SSH a Linux-alapú virtuális gépek](../linux/classic/reset-access-classic.md).
