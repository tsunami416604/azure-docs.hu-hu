---
title: Az Azure DevTest Labs összetevőivel kapcsolatos problémák elhárítása | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el az Azure DevTest Labs virtuális gépeken összetevők alkalmazásakor felmerülő problémákat.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456987"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Az Azure DevTest Labs virtuális gépeken lévő összetevők alkalmazásakor felmerülő problémák elhárítása
A virtuális gépen lévő összetevők alkalmazása különböző okok miatt sikertelen lehet. Ez a cikk végigvezeti a lehetséges okok azonosítását segítő módszereken.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure DevTest Labs (DTL) szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget.   

> [!NOTE]
> Ez a cikk windowsos és nem Windows rendszerű virtuális gépekre egyaránt vonatkozik. Bár vannak különbségek, akkor kifejezetten ki kell hívni ebben a cikkben.

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Ellenőrizze, hogy a virtuális gép fut-e. DevTest Labs megköveteli, hogy a virtuális gép fut, és hogy a [Microsoft Azure virtuálisgép-ügynök (VM-ügynök)](../virtual-machines/extensions/agent-windows.md) telepítve van, és készen áll.

> [!TIP]
> Az **Azure Portalon**keresse meg az **összetevők kezelése** lapot a virtuális gép, hogy a virtuális gép készen áll az összetevők alkalmazására. Megjelenik egy üzenet az oldal legtetején. 
> 
> Az **Azure PowerShell**használatával vizsgálja meg a jelző **canApplyArtifacts**, amely csak akkor adja vissza, ha egy GET művelet bővítése. Lásd a következő példaparancsot:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>A hibaelhárítás módjai 
A DevTest Labs és az Erőforrás-kezelő telepítési modell használatával létrehozott virtuális gépek hibaelhárítása az alábbi módszerek egyikével:

- **Azure Portal** – nagyszerű, ha gyorsan vizuális képet kell kapnia arról, hogy mi okozhatja a problémát.
- **Azure PowerShell** – ha egy PowerShell-parancshasználatával elégedett, gyorsan lekérdezheti a DevTest Labs-erőforrásokat az Azure PowerShell-parancsmagok használatával.

> [!TIP]
> A virtuális gépen belüli műtermék-végrehajtás áttekintése című témakörben talál további információt [a műtermék-hibák diagnosztizálása a laborban című témakörben.](devtest-lab-troubleshoot-artifact-failure.md)

## <a name="symptoms-causes-and-potential-resolutions"></a>Tünetek, okok és lehetséges felbontások 

### <a name="artifact-appears-to-hang"></a>Úgy tűnik, hogy az ereklye lefagy   
Úgy tűnik, hogy egy összetevő lefagy, amíg egy előre meghatározott időelmiszerződés le nem jár, és a műtermék sikertelenként van **megjelölve.**

Amikor egy lelet lefagy, először határozza meg, hol ragadt meg. Az összetevők a végrehajtás során az alábbi lépések bármelyikén blokkolhatók:

- **Az első kérelem során**. A DevTest Labs létrehoz egy Azure Resource Manager-sablont az egyéni parancsfájl-bővítmény (CSE) használatához. Ezért a színfalak mögött egy erőforráscsoport üzembe helyezése aktiválódik. Ha egy hiba ezen a szinten történik, a részleteket a **tevékenységnaplók** az erőforráscsoport a szóban forgó virtuális gép.  
    - A tevékenységnaplót a labor virtuális gép lap navigációs sávjáról érheti el. Amikor kiválasztja, megjelenik egy bejegyzés vagy **összetevők alkalmazása a virtuális gépre** (ha az alkalmazási összetevők művelet közvetlenül aktiválódott) vagy hozzáadása vagy módosítása a virtuális **gépek** (ha az alkalmazási összetevők művelet része volt a virtuális gép létrehozása folyamat).
    - Keresse meg a hibákat ezekben a bejegyzésekben. Néha a hiba nem lesz ennek megfelelően címkézve, és minden bejegyzést meg kell vizsgálnia.
    - Az egyes bejegyzések részleteinek vizsgálatakor ellenőrizze a JSON-tartalom tartalmát. Előfordulhat, hogy a dokumentum alján hibaüzenet jelenik meg.
- **Amikor megpróbálja végrehajtani a műtárgyat**. Ez lehet az oka a hálózati vagy tárolási problémák. A részleteket lásd a cikk későbbi részében található megfelelő részben. Ez is megtörténhet, mert az utat a szkript szerzője. Példa:
    - A PowerShell-parancsfájlok **kötelező paraméterekkel**rendelkeznek, de nem ad nak át neki értéket, vagy azért, mert engedélyezi a felhasználószámára, hogy üresen hagyja, vagy mert nincs alapértelmezett értéke a tulajdonságnak az artifactfile.json definíciós fájlban. A parancsfájl lefagy, mert felhasználói bevitelre vár.
    - Egy PowerShell-parancsfájl a végrehajtás részeként **felhasználói beavatkozást igényel.** A parancsfájlokat úgy kell megírni, hogy felhasználói beavatkozás nélkül működjenek csendben.
- **VM ügynök sokáig tart, hogy készen álljon**. Amikor a virtuális gép először indul el, vagy amikor az egyéni parancsfájl-bővítmény először telepítve van az összetevők alkalmazásával való kérelem kiszolgálására, a virtuális gép kérheti a virtuálisgép-ügynök frissítését, vagy megvárja a virtuálisgép-ügynök inicializálását. Előfordulhat, hogy a virtuális gép ügynöke, amelyektől függ, hogy a virtuális gép ügynöke, amelyek inicializálása hosszú időt vesz igénybe. Ilyen esetekben tekintse meg [az Azure Virtual Machine Agent áttekintése](../virtual-machines/extensions/agent-windows.md) további hibaelhárítás.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Annak ellenőrzése, hogy a műtermék a parancsfájl miatt lefagy-e

1. Jelentkezzen be a kérdéses virtuális gépbe.
2. Másolja a parancsfájlt helyileg a virtuális gépre, `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`vagy keresse meg a virtuális gépen a területen. Ez az a hely, ahol a műtermék-parancsfájlok letöltődnek.
3. Rendszergazda jogú parancssor használatával helyileg hajtsa végre a parancsfájlt, és adja meg a probléma okozó paraméterértékeit.
4. Határozza meg, hogy a parancsfájl nem kívánt viselkedést szenved-e. Ha igen, vagy kérjen frissítést a műtermékhez (ha az a nyilvános tárműtérből származik); vagy saját maga kell elkészítenia a javításokat (ha az a saját adattárából származik).

> [!TIP]
> Kijavíthatja a [nyilvános tárházban](https://github.com/Azure/azure-devtestlab) tárolt műtermékekkel kapcsolatos problémákat, és elküldheti a módosításokat az ellenőrzésünkre és jóváhagyásunkra. Tekintse meg a README.md [dokumentum](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) **Hozzájárulás** szakaszát.
> 
> A saját összetevők írásával kapcsolatos tudnivalókért [tekintse meg AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) dokumentumban.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Annak ellenőrzése, hogy a műtermék a virtuális gép ügynöke miatt lefagy-e:
1. Jelentkezzen be a kérdéses virtuális gépbe.
2. A Fájlkezelő segítségével keresse meg a **C:\WindowsAzure\logs mappát.**
3. Keresse meg és nyissa meg a **WaAppAgent.log**fájlt.
4. Keresse meg azolyan bejegyzéseket, amelyek a virtuálisgép-ügynök indításakor és az inicializálás befejezésekor (azaz az első szívverés elküldésével) jelennek meg. Előnyben részesíti az újabb bejegyzéseket, vagy különösen azokat, amelyek körül az időszak, amelyre a problémát tapasztalja.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    Ebben a példában láthatja, hogy a virtuális gép ügynök kezdési ideje 10 perc és 20 másodperc, mert a szívverés elküldésre került. Az ok ebben az esetben az OOBE szolgáltatás indítása hosszú időt vesz igénybe.

> [!TIP]
> Az Azure-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások](../virtual-machines/extensions/overview.md)című témakörben talál általános tudnivalókat.

## <a name="storage-errors"></a>Tárolási hibák
DevTest Labs hozzáférést igényel a labor tárfiók, amely a gyorsítótár-összetevők gyorsítótárazása érdekében létrehozott. Amikor a DevTest Labs egy műtermék, beolvassa a műtermék konfigurációját és a fájlokat a konfigurált tárházakból. Alapértelmezés szerint a DevTest Labs konfigurálja a **nyilvános műtermék-tárlathoz**való hozzáférést.

Attól függően, hogy a virtuális gép van konfigurálva, előfordulhat, hogy nem rendelkezik közvetlen hozzáféréssel a tárcsa. Ezért a DevTest Labs szándékosan gyorsítótárazza az összetevőkegy tárfiókban, amely a labor első inicializálásakor jön létre.

Ha a tárfiókhoz való hozzáférés le van tiltva bármilyen módon, mint amikor a forgalom le van tiltva a virtuális gép az Azure Storage szolgáltatás, előfordulhat, hogy a következőhöz hasonló hibaüzenet jelenhet meg:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

A fenti hiba az Összetevők kezelése csoport **Műtermék-eredmények** lapjának **Telepítési üzenet** szakaszában jelenik **meg.** A **tevékenységnaplókban** is megjelenik a szóban forgó virtuális gép erőforráscsoportja alatt.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Annak biztosítása érdekében, hogy az Azure Storage-szolgáltatással való kommunikáció ne legyen letiltva:

- **Ellenőrizze a hozzáadott hálózati biztonsági csoportok (NSG)**. Előfordulhat, hogy egy előfizetési szabályzat került hozzáadásra, ahol az NSG-k automatikusan konfigurálva vannak az összes virtuális hálózatban. Ez is hatással lenne a labor alapértelmezett virtuális hálózat, ha használják, vagy más virtuális hálózat konfigurálva a laborban, virtuális gépek létrehozásához használt.
- **Ellenőrizze az alapértelmezett labor tárfiók** (azaz az első tárfiók létrehozásakor a labor létrehozásakor, akinek a neve általában kezdődik a\<"a" betű, és végződik egy többjegyű szám, amely egy labname\>#).
    1. Keresse meg a labor erőforráscsoportját.
    2. Keresse meg azt a **típusú tárolófiókot,** amelynek neve megegyezik a konvencióval.
    3. Nyissa meg a **Tűzfalak és virtuális hálózatok**nevű tárfióklapot.
    4. Győződjön meg arról, hogy minden **hálózat**ra van állítva. Ha a **Kiválasztott hálózatok** beállítás be van jelölve, akkor győződjön meg arról, hogy a tesztkörnyezet virtuális hálózatok létrehozásához használt virtuális hálózatok hozzáadódnak a listához.

További részletes hibaelhárításért olvassa [el az Azure Storage tűzfalainak és virtuális hálózatainak konfigurálása című témakört.](../storage/common/storage-network-security.md)

> [!TIP]
> **Ellenőrizze a hálózati biztonsági csoport szabályait**. Az [IP-folyamat ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) segítségével ellenőrizze, hogy egy hálózati biztonsági csoport egyik szabálya blokkolja-e a virtuális gépre irányuló vagy onnan érkező forgalmat. A hatályos biztonságicsoport-szabályokat is **Allow** áttekintheti, hogy a bejövő NSG-engedélyezése szabály létezik-e. További információ: [Hatékony biztonsági szabályok használata a virtuális gépek forgalmának elhárításához.](../virtual-network/diagnose-network-traffic-filter-problem.md)

## <a name="other-sources-of-error"></a>Egyéb hibaforrások
Vannak más, ritkábban lehetséges hibaforrások is. Győződjön meg róla, hogy értékelje az egyes, hogy ha vonatkozik az Ön esetében. Itt van az egyik: 

- **A privát tártár lejárt személyes hozzáférési jogkivonata.** Ha lejárt, a műtermék nem jelenik meg a listában, és minden olyan parancsfájlok, amelyek egy lejárt magán-hozzáférési jogkivonattal rendelkező tárház összetevőire hivatkoznak, ennek megfelelően sikertelenek lesznek.

## <a name="next-steps"></a>További lépések
Ha a hibák egyike sem történt, és továbbra sem tudja alkalmazni az összetevőket, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

