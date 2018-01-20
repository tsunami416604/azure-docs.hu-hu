---
title: "Egy Azure virtuális gép SSH-kapcsolati problémák elhárítása |} Microsoft Docs"
description: "Hibák elhárításához, például \"Az SSH-kapcsolat sikertelen\" vagy \"Az SSH-kapcsolat elutasítva\" egy Azure virtuális gép Linux operációs rendszert futtató módjáról."
keywords: "ssh kapcsolat visszautasította, ssh hiba és az azure-ssh, SSH-kapcsolat sikertelen"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: b7fe6dadb444ebbe6af6239562f507e451f9f605
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Az Azure Linux virtuális gép, amely nem sikerül, hibák, vagy elutasítják SSH-kapcsolatok hibáinak elhárítása
Oka lehet különböző, hogy Secure Shell (SSH) hibák, az SSH-kapcsolódási hibák, vagy az SSH a rendszer elutasította a rendszer, amikor egy Linux virtuális gép (VM) csatlakozni próbál. Ez a cikk segít keresse meg és javítsa ki a problémákat. Az Azure-portálon az Azure parancssori felület vagy a Linux virtuális gép hozzáférési bővítményével hibakeresésre és problémák megoldásához használható.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](http://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](http://azure.microsoft.com/support/options/) válassza **segítségre van szüksége**. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Első hibaelhárítási lépések
Hibaelhárítási lépések, után próbáljon újra csatlakozni a virtuális Gépet.

1. Visszaállítja az SSH-konfigurációt.
2. A felhasználó hitelesítő adatainak visszaállítása.
3. Ellenőrizze a [hálózati biztonsági csoport](../../virtual-network/virtual-networks-nsg.md) szabályok lehetővé teszik az SSH-forgalmat.
   * Győződjön meg arról, hogy létezik-e a hálózati biztonsági csoport szabály az SSH-forgalom engedélyezése (ami alapértelmezés szerint a 22-es TCP-port).
   * Nem használhat port átirányítási / leképezése egy Azure terheléselosztó használata nélkül.
4. Ellenőrizze a [VM erőforrás állapota](../../resource-health/resource-health-overview.md). 
   * Győződjön meg arról, hogy a virtuális gép jelenti, hogy kifogástalan-e.
   * Ha engedélyezve van a rendszerindítási diagnosztika, győződjön meg arról, a virtuális gép nem jelent a naplókban hibák.
5. Indítsa újra a virtuális gépet.
6. Telepítse újra a virtuális Gépet.

Részletesebb hibaelhárítási lépéseket és magyarázatokat olvasási továbbra is.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Rendelkezésre álló metódusok SSH-kapcsolati problémák elhárítása
Új hitelesítő adatokat, vagy SSH-konfigurációt az alábbi módszerek egyikének használatával:

* [Azure-portálon](#use-the-azure-portal) - nagyszerű, ha az SSH-konfigurációt vagy SSH-kulcs gyorsan alaphelyzetbe kell, és nem kell az Azure-eszközök telepítve.
* [Az Azure CLI 2.0](#use-the-azure-cli-20) – Ha már a parancssorban, gyorsan alaphelyzetbe az SSH-konfigurációt vagy a hitelesítő adatokat. Használhatja a [Azure CLI 1.0](#use-the-azure-cli-10)
* [Az Azure VMAccessForLinux bővítmény](#use-the-vmaccess-extension) – hozzon létre, és újra felhasználhatja a json-definíciós fájlokat, és az SSH konfigurációs vagy felhasználói hitelesítő adatok alaphelyzetbe állítása.

Minden hibaelhárítási lépés után ismét kapcsolódni a virtuális Gépre próbálja. Ha továbbra sem sikerül kapcsolódni, próbálja meg a következő lépéssel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata
Az Azure-portál a helyi számítógépen bármely eszközök telepítése nélkül a SSH vagy felhasználói hitelesítő adatok alaphelyzetbe állítása gyors lehetőséget kínál.

Válassza ki a virtuális Gépet az Azure portálon. Görgessen le a **támogatási + hibaelhárítás** válassza ki azt **jelszó-átállítási** az alábbi példában látható módon:

![Új SSH-konfigurációt vagy a hitelesítő adatokat az Azure-portálon](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Az SSH-konfigurációjának visszaállítása
Első lépésként válassza ki a `Reset configuration only` a a **mód** legördülő menü, az előző képernyőképet, majd kattintson a **alaphelyzetbe** gombra. Ez a művelet befejezése után próbálja meg ismét elérni a virtuális Gépet.

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó a SSH hitelesítő adatok alaphelyzetbe állítása
Válassza a hitelesítő adatokat egy meglévő felhasználó alaphelyzetbe állításához `Reset SSH public key` vagy `Reset password` a a **mód** legördülő menü, ahogy az előző képernyőképet. Adja meg a felhasználónevet és egy SSH-kulcs vagy új jelszót, majd kattintson a **alaphelyzetbe** gombra.

A felhasználó sudo jogosultsági szintű ebben a menüben a virtuális gép is létrehozhat. Adjon meg egy új felhasználónevet és a kapcsolódó jelszó vagy SSH-kulcs, és kattintson a **alaphelyzetbe** gombra.

## <a name="use-the-azure-cli-20"></a>Az Azure parancssori felület használatával 2.0
Ha még nem tette meg, telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/#login).

Ha létrehozott és Linux egyéni lemezképet feltöltve, ellenőrizze, hogy a [Microsoft Azure Linux ügynök](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 2.0.5 verzió vagy újabb verziója. A gyűjtemény lemezképei használatával létrehozott virtuális gép esetében a hozzáférés bővítmény már telepített és konfigurálást.

### <a name="reset-ssh-configuration"></a>SSH-konfigurációjának visszaállítása
Először is próbálja meg az SSH-konfigurációjának visszaállítása az alapértelmezett értékekre, és az SSH-kiszolgáló a virtuális gép újraindul. Vegye figyelembe, hogy ez nem módosítja a felhasználói fiók nevét, jelszó vagy SSH-kulcsok.
Az alábbi példában [az vm felhasználói alaphelyzetbe állítása-ssh](/cli/azure/vm/user#reset-ssh) nevű virtuális gép SSH-konfigurációjának visszaállítása `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó a SSH hitelesítő adatok alaphelyzetbe állítása
Az alábbi példában [az vm felhasználói frissítése](/cli/azure/vm/user#update) alaphelyzetbe állítani a hitelesítő adatokat a `myUsername` a megadott értékre `myPassword`, a nevű virtuális Gépre `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Ha SSH-kulcs hitelesítést használ, visszaállíthatja az SSH-kulcs az adott felhasználó számára. Az alábbi példában **az vm hozzáférés set-linux-felhasználói** frissíteni az SSH-kulcs tárolható `~/.ssh/id_rsa.pub` nevű felhasználó `myUsername`, a nevű virtuális Gépre `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Használja a VMAccess bővítmény
A Linux virtuális gép hozzáférési bővítményével beolvassa a json-fájl, amely meghatározza a műveletek elvégzéséhez. Ilyen műveletek közé tartoznak a SSHD alaphelyzetbe állítása, egy SSH-kulcsának visszaállítása vagy egy felhasználót. Továbbra is használhatja az Azure parancssori felület a VMAccess bővítmény hívására, de is felhasználhatja a json-fájlok több virtuális gépek között, szükség esetén. Ez a megközelítés lehetővé teszi majd hívható forgatókönyvek a megadott json-fájlokat egy gyűjteményének létrehozásához.

### <a name="reset-sshd"></a>SSHD alaphelyzetbe állítása
Hozzon létre egy fájlt `settings.json` a következő tartalommal:

```json
{  
    "reset_ssh":"True"
}
```

Az Azure parancssori felület használatával, majd meghívja a `VMAccessForLinux` bővítmény a SSHD kapcsolat alaphelyzetbe állítására a json-fájl megadásával. Az alábbi példában [az virtuálisgép-bővítmény készlet](/cli/azure/vm/extension#set) alaphelyzetbe állítja a nevű virtuális Gépre SSHD `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó a SSH hitelesítő adatok alaphelyzetbe állítása
SSHD úgy tűnik, hogy megfelelően működik, ha alaphelyzetbe állíthatja a előállító felhasználó hitelesítő adatait. A felhasználó jelszavának alaphelyzetbe állításához hozzon létre egy fájlt `settings.json`. Az alábbi példában a hitelesítő adatok alaphelyzetbe állítása `myUsername` a megadott értékre `myPassword`. Adja meg a következő sorokat a `settings.json` fájl, a saját értékekkel:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Az SSH-kulcs egy felhasználó alaphelyzetbe állításához először hozzon létre egy fájlt vagy `settings.json`. Az alábbi példában a hitelesítő adatok alaphelyzetbe állítása `myUsername` a megadott értékre `myPassword`, a virtuális Gépre nevű `myVM` a `myResourceGroup`. Adja meg a következő sorokat a `settings.json` fájl, a saját értékekkel:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

A json-fájl létrehozása után használja az Azure parancssori felület hívásához a `VMAccessForLinux` állítsa alaphelyzetbe az SSH hitelesítő adatait adja meg a json-fájl kiterjesztése. Az alábbi példa alaphelyzetbe állítja a virtuális gép nevű hitelesítő adatok `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Az Azure parancssori felület használatával 1.0
Ha még nem tette, [telepítse az Azure CLI 1.0, és csatlakozzon az Azure-előfizetéshez](../../cli-install-nodejs.md). Győződjön meg arról, hogy a Resource Manager módot az alábbiak szerint:

```azurecli
azure config mode arm
```

Ha létrehozott és Linux egyéni lemezképet feltöltve, ellenőrizze, hogy a [Microsoft Azure Linux ügynök](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 2.0.5 verzió vagy újabb verziója. A gyűjtemény lemezképei használatával létrehozott virtuális gép esetében a hozzáférés bővítmény már telepített és konfigurálást.

### <a name="reset-ssh-configuration"></a>SSH-konfigurációjának visszaállítása
Nincs megfelelően konfigurálva a SSHD konfigurációs magát, vagy a szolgáltatás hibát észlelt. Ellenőrizze, hogy az SSH-konfigurációt maga érvényes SSHD alaphelyzetbe állíthatja. SSHD alaphelyzetbe kell lennie az első hibaelhárítási lépés végrehajtása.

Az alábbi példa alaphelyzetbe állítja a virtuális gép neve SSHD `myVM` az erőforráscsoport neve `myResourceGroup`. A saját virtuális gép és erőforrás-neveket használja az alábbiak szerint:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Egy felhasználó a SSH hitelesítő adatok alaphelyzetbe állítása
SSHD úgy tűnik, hogy megfelelően működik, ha az előállító felhasználó jelszavának alaphelyzetbe állíthatja. Az alábbi példában a hitelesítő adatok alaphelyzetbe állítása `myUsername` a megadott értékre `myPassword`, a virtuális Gépre nevű `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Ha SSH-kulcs hitelesítést használ, visszaállíthatja az SSH-kulcs az adott felhasználó számára. Az alábbi példa frissíti az SSH-kulcs tárolható `~/.ssh/id_rsa.pub` nevű felhasználó `myUsername`, a nevű virtuális Gépre `myVM` a `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Virtuális gép újraindítása
Ha az SSH konfigurációs és a felhasználói hitelesítő adatok alaphelyzetbe állítása, vagy ennek során hibába ütközött, próbálkozhat újraindítással a virtuális gép az alapul szolgáló számítási problémák címre.

### <a name="azure-portal"></a>Azure Portal
Indítsa újra a virtuális gépek az Azure portál használatával, jelölje ki a virtuális Gépet, majd kattintson a **indítsa újra a** gombra az alábbi példában látható módon:

![Indítsa újra a virtuális gépek Azure-portálon](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
Az alábbi példa nevű virtuális gép újraindul `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az alábbi példában [az virtuális gép újraindítása](/cli/azure/vm#restart) nevű virtuális gép újraindítására `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Virtuális gép ismételt üzembe helyezése
Egy virtuális Gépet az Azure, amely előfordulhat, hogy javítsa ki a mögöttes hálózati probléma merül fel egy másik csomópontra telepítheti. További információ a virtuális gép újbóli: [újratelepíteni a virtuális gépet az új Azure csomópont](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Ez a művelet befejezése után rövid élettartamú lemezen tárolt adatok ekkor elvesznek, és dinamikus IP-címek, amelyek a virtuális géphez kapcsolódó frissülni fog.
> 
> 

### <a name="azure-portal"></a>Azure Portal
Újratelepíteni a virtuális gépek az Azure portál használatával, jelölje ki a virtuális Gépet, és görgessen le a **támogatási + hibaelhárítás** szakasz. Kattintson a **újratelepíteni** gombra az alábbi példában látható módon:

![Telepítse újra a virtuális gépek Azure-portálon](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
Az alábbi példa redeploys nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
A következő használja például [az vm helyezze üzembe újra](/cli/azure/vm#redeploy) újratelepíteni a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`. A saját értékeit a következőképpen használhatja:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>A klasszikus telepítési modell használatával létrehozott virtuális gépek
Próbálja meg ezeket a lépéseket a klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek a leggyakrabban használt SSH-kapcsolódási hibák megoldására. Minden lépés után próbáljon újra csatlakozni a virtuális Gépet.

* A távelérés alaphelyzetbe állítása a [Azure-portálon](https://portal.azure.com). Az Azure portálon, válassza ki a virtuális Gépet, majd kattintson a **távoli alaphelyzetbe állítása...**  gombra.
* Indítsa újra a virtuális gépet. Az a [Azure-portálon](https://portal.azure.com), jelölje ki a virtuális Gépet, és kattintson a **indítsa újra a** gombra.
    
* Telepítse újra a virtuális Gépet egy új Azure csomópontra. Telepítse újra a virtuális gépek kapcsolatos információkért lásd: [újratelepíteni a virtuális gépet az új Azure csomópont](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Ez a művelet befejezése után rövid élettartamú lemezen tárolt adatok ekkor elvesznek, és dinamikus IP-címek, amelyek a virtuális géphez kapcsolódó frissülni fog.
* Kövesse az utasításokat a [alaphelyzetbe állításával a jelszó vagy SSH a Linux-alapú virtuális gépek](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) számára:
  
  * A jelszó vagy SSH-kulcs visszaállítása.
  * Hozzon létre egy *sudo* felhasználói fiókot.
  * Visszaállítja az SSH-konfigurációt.
* Ellenőrizze a VM erőforrás állapota a platform probléma merül fel.<br>
     Válassza ki a virtuális Gépet, és görgessen lefelé **beállítások** > **állapotának ellenőrzése**.

## <a name="additional-resources"></a>További források
* Ha SSH továbbra sem tudja a virtuális géphez után lépések végrehajtása után, lásd: [részletes hibaelhárítási lépéseket](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) való tekintse át a probléma megoldásához szükséges további lépéseket.
* Alkalmazás-hozzáférés hibaelhárítással kapcsolatos további információkért lásd: [egy Azure virtuális gépen futó alkalmazáshoz való hozzáférés hibáinak elhárítása](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek hibaelhárítással kapcsolatos további információkért lásd: [alaphelyzetbe állításával a jelszó vagy SSH a Linux-alapú virtuális gépek](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

