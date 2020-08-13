---
title: Hibrid gép összekapcsolása az Azure arc-kompatibilis kiszolgálókkal
description: Ismerje meg, hogyan csatlakozhat és regisztrálhat hibrid gépet a kiszolgálókhoz készült Azure arc használatával.
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: 2feb9da3600ef3664b0a75bf2d985d698572ff6f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173128"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Gyors útmutató: hibrid gép összekapcsolása az Azure arc-kompatibilis kiszolgálókkal

Az [Azure arc for Servers](../overview.md) (előzetes verzió) lehetővé teszi a helyszíni, Edge és többfelhős környezetekben üzemeltetett Windows és Linux rendszerű gépek felügyeletét és szabályozását. Ebben a rövid útmutatóban az Azure-on kívül üzemeltetett Windows-vagy Linux-gépen a csatlakoztatott számítógép-ügynök üzembe helyezését és konfigurálását végzi a kiszolgálók számára (előzetes verzió).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Az arc for Servers (előzetes verzió) hibrid csatlakoztatott számítógép ügynökének üzembe helyezéséhez rendszergazdai engedélyekkel kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiókkal és a Windows rendszeren egy olyan fiókkal, amely a helyi Rendszergazdák csoport tagja.

* Az első lépések előtt tekintse át az ügynök [előfeltételeit](../agent-overview.md#prerequisites) , és ellenőrizze a következőket:

    * A célszámítógép támogatott [operációs rendszert](../agent-overview.md#supported-operating-systems)futtat.

    * A fiókja megkapja a [szükséges Azure-szerepkörök](../agent-overview.md#required-permissions)hozzárendelését.

    * Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, győződjön meg arról, hogy a [felsorolt](../agent-overview.md#networking-configuration) URL-címek nincsenek letiltva.

    * Az Azure arc for Servers (előzetes verzió) csak az [itt](../overview.md#supported-regions)megadott régiókat támogatja.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure-erőforrás-szolgáltatók regisztrálása

Az Azure arc for Servers (előzetes verzió) az előfizetés alábbi Azure-erőforrás-szolgáltatói függ a szolgáltatás használatához:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Regisztrálja őket a következő parancsok használatával:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Telepítési parancsfájl létrehozása

Az Azure arc letöltésének, telepítésének és létrehozásának automatizálására szolgáló parancsfájl a Azure Portal érhető el. A folyamat befejezéséhez tegye a következőket:

1. Indítsa el az Azure arc szolgáltatást a Azure Portal a **minden szolgáltatás**elemre kattintva, majd a gépek keresése és kiválasztása **– Azure arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="A kiszolgálók ív keresése az összes szolgáltatásban" border="false":::

1. A **gépek – Azure arc** lapon válassza a **Hozzáadás**, a bal felső sarokban vagy a **számítógép létrehozása – Azure arc** lehetőséget a középső ablaktábla alján.

1. A **módszer kiválasztása** lapon jelölje be a **számítógépek hozzáadása interaktív parancsfájl** -csempével lehetőséget, majd válassza a **parancsfájl létrehozása**lehetőséget.

1. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja.

1. A **parancsfájl létrehozása** lap **operációs rendszer** legördülő listájában válassza ki azt az operációs rendszert, amelyen a parancsfájl futni fog.

1. Ha a gép proxykiszolgálón keresztül kommunikál az internethez, válassza a **Tovább: proxykiszolgáló**lehetőséget.

1. A **proxykiszolgáló** lapon adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép a proxykiszolgálóhoz való kommunikációhoz használni fog. Adja meg az értéket a formátumban `http://<proxyURL>:<proxyport>` .

1. Válassza a **felülvizsgálat + előállítás**lehetőséget.

1. A **felülvizsgálat + előállítás** lapon tekintse át az összegző információkat, majd kattintson a **Letöltés**gombra. Ha továbbra is módosításokat kell végeznie, válassza az **előző**lehetőséget.

## <a name="install-the-agent-using-the-script"></a>Az ügynök telepítése a parancsfájl használatával

### <a name="windows-agent"></a>Windows-ügynök

1. Jelentkezzen be a kiszolgálóra.

1. Nyisson meg egy emelt szintű 64-bites PowerShell-parancssort.

1. Váltson arra a mappára vagy megosztásra, amelyre a parancsfájlt másolta, majd futtassa azt a kiszolgálón a parancsfájl futtatásával `./OnboardingScript.ps1` .

### <a name="linux-agent"></a>Linux-ügynök

1. Az alábbi parancs futtatásával telepítheti a Linux-ügynököt azon a célszámítógépen, amely közvetlenül tud kommunikálni az Azure-ban:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Ha a célszámítógép egy proxykiszolgálón keresztül kommunikál, futtassa a következő parancsot:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és úgy konfigurálja, hogy az Azure arc for Servers (előzetes verzió) szolgáltatáshoz kapcsolódjon, lépjen a Azure Portal a kiszolgáló sikeres csatlakoztatásának ellenőrzéséhez. Megtekintheti a gépet a [Azure Portalban](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="A számítógép sikeres csatlakoztatása" border="false":::

## <a name="next-steps"></a>További lépések

Most, hogy engedélyezte a Linux vagy Windows rendszerű hibrid gépet, és sikeresen csatlakozott a szolgáltatáshoz, készen áll a Azure Policy az Azure-ban való megfelelőség megismerésére.

Ha meg szeretné tudni, hogyan azonosíthatók az Azure arc olyan kiszolgálók (előzetes verzió) számára engedélyezett gépek, amelyeken nincs telepítve a Log Analytics ügynök, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához](tutorial-assign-policy-portal.md)
