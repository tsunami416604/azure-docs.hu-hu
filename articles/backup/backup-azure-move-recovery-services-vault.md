---
title: Azure Backup Recovery Services-tárolók áthelyezése
description: Útmutatás az Recovery Services-tárolók Azure-előfizetések és-erőforráscsoportok közötti áthelyezéséhez.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: b4de4c58e6a6551fa7c27db9755bb02a11700721
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755738"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Recovery Services-tároló áthelyezése az Azure-előfizetések és-erőforráscsoportok között

Ez a cikk azt ismerteti, hogyan helyezhet át egy Recovery Services-tárolót az Azure-előfizetések között Azure Backuphoz, vagy egy másik erőforráscsoporthoz ugyanabban az előfizetésben. Recovery Services-tároló áthelyezéséhez a Azure Portal vagy a PowerShellt használhatja.

## <a name="supported-regions"></a>Támogatott régiók

A Recovery Services-tár erőforrás-áthelyezését a Kelet-Ausztrália támogatja. Délkelet-Ausztrália, Közép-Kanada, Kelet-Kanada, Dél-Kelet-Ázsia, Kelet-Ázsia, Közép-USA, USA északi középső régiója, USA keleti régiója, USA 2. keleti régiója, USA déli középső régiója, az USA nyugati középső régiója, USA nyugati régiója, USA 2. nyugati régiója, Közép-India, Dél-India, Kelet-Japán , Nyugat-Európa, Dél-Afrika északi régiója, Dél-Afrika nyugati régiója, Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója.

## <a name="unsupported-regions"></a>Nem támogatott régiók

Közép-Franciaország, Dél-Franciaország, Északkelet-Németország, Közép-Németország, US Gov Iowa, Észak-Kína, Kína North2, Kelet-Kína, Kína készletek

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Recovery Services-tároló áthelyezésének előfeltételei

- A tárolóban az erőforráscsoportok között a forrás-és a célként megadott erőforráscsoportok is zárolva vannak az írási és törlési műveletek megakadályozása érdekében. További információkért tekintse meg [ezt a cikket](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Csak a rendszergazdai előfizetés rendelkezik a tároló áthelyezéséhez szükséges engedélyekkel.
- A tárolók előfizetések közötti áthelyezéséhez a cél előfizetésnek ugyanabban a bérlőben kell lennie, mint a forrás-előfizetésnek, és annak állapotát engedélyezni kell.
- A cél erőforráscsoporthoz írási műveletek végrehajtásához engedéllyel kell rendelkeznie.
- A tár áthelyezése csak az erőforráscsoportot módosítja. A Recovery Services-tároló ugyanazon a helyen fog megjelenni, és nem módosítható.
- Régiónként egyszerre csak egy Recovery Services-tárolót lehet áthelyezni.
- Ha egy virtuális gép nem a Recovery Services-tárolóval, vagy egy új erőforráscsoporthoz kerül át, akkor az aktuális virtuálisgép-helyreállítási pontok sértetlenek maradnak a tárolóban mindaddig, amíg lejárnak.
- Azt jelzi, hogy a virtuális gép a tárolóval van-e áthelyezve, vagy sem, bármikor visszaállíthatja a virtuális gépet a tár megőrzött biztonsági mentési előzményeiből.
- A Azure Disk Encryption megköveteli, hogy a Key Vault és a virtuális gépek ugyanabban az Azure-régióban és-előfizetésben legyenek tárolva.
- Ha felügyelt lemezekkel rendelkező virtuális gépet szeretne áthelyezni, tekintse meg ezt a [cikket](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- A klasszikus modellben üzembe helyezett erőforrások áthelyezésének lehetőségei eltérnek attól függően, hogy az adott előfizetésen belül vagy egy új előfizetésre helyezi át az erőforrásokat. További információkért tekintse meg [ezt a cikket](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- A tárolóhoz definiált biztonsági mentési szabályzatok megmaradnak, miután a tár előfizetések vagy egy új erőforráscsoport között mozog.
- Csak olyan tárolót helyezhet át, amely a következő típusú biztonsági másolati elemeket tartalmazza. Az alább nem felsorolt típusú biztonsági másolati elemeket le kell állítani, és az adatokat véglegesen törölni kell a tár áthelyezése előtt.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services (MARS) ügynök
  - Microsoft Azure Backup-kiszolgáló (MABS)
  - Data Protection Manager (DPM)
- Ha egy virtuális gép biztonsági mentési adatait tartalmazó tárolót helyez át az előfizetések között, át kell helyeznie a virtuális gépeket ugyanahhoz az előfizetéshez, és ugyanazt a cél virtuálisgép-erőforráscsoport nevét kell használnia (mint a régi előfizetésben) a biztonsági mentések folytatásához.

> [!NOTE]
> Az Azure-régiók Azure Backup Recovery Services-tárolóinak áthelyezése nem támogatott.<br><br>
> Ha a **Azure site Recovery**használatával bármilyen virtuális gépet (Azure IaaS, Hyper-V, VMware) vagy fizikai gépet konfigurált, akkor az áthelyezési művelet le lesz tiltva. Ha Azure Site Recovery tárolóit szeretné áthelyezni, tekintse át [ezt a cikket](../site-recovery/move-vaults-across-regions.md) , és ismerkedjen meg a tárolók manuális mozgatásával.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Recovery Services-tár másik erőforráscsoporthoz való áthelyezéséhez használja a Azure Portal

Recovery Services-tároló és a hozzá tartozó erőforrások áthelyezése különböző erőforráscsoporthoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg **Recovery Services** -tárolók listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultja, az a következő képen látható módon jelenik meg.

   ![Recovery Services-tároló megnyitása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Ha nem látja a tár **alapvető** információit, válassza a legördülő ikont. Ekkor látnia kell a tár alapvető információit.

   ![Alapvető információk lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintése menüben kattintson az **erőforráscsoport**melletti **módosítás** elemre az **erőforrások áthelyezése** panel megnyitásához.

   ![Erőforráscsoport módosítása](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Az **erőforrások áthelyezése** ablaktáblán a kiválasztott tároló esetében ajánlott áthelyezni a választható kapcsolódó erőforrásokat a jelölőnégyzet bejelölésével az alábbi ábrán látható módon.

   ![Előfizetés áthelyezése](./media/backup-azure-move-recovery-services/move-resource.png)

5. A cél erőforráscsoport hozzáadásához az **erőforráscsoport** legördülő listában válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új csoport létrehozása** lehetőséget.

   ![Erőforrás létrehozása](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Az erőforráscsoport hozzáadása után erősítse meg, **hogy az áthelyezett erőforrásokhoz társított eszközök és parancsfájlok nem fognak működni, amíg nem frissítem őket az új erőforrás-azonosítók** lehetőségre, majd kattintson **az OK** gombra a tár áthelyezésének befejezéséhez.

   ![Megerősítő üzenet](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Recovery Services-tároló áthelyezése másik előfizetésre a Azure Portal használatával

Egy Recovery Services-tárolót és a hozzá tartozó erőforrásokat egy másik előfizetésbe helyezheti át

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg Recovery Services-tárolók listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultja, az a következő képen látható módon jelenik meg.

    ![Recovery Services-tároló megnyitása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Ha nem látja a tár **alapvető** információit, válassza a legördülő ikont. Ekkor látnia kell a tár alapvető információit.

    ![Alapvető információk lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintő menüjében válassza az **előfizetés**melletti **módosítás** lehetőséget az **erőforrások áthelyezése** panel megnyitásához.

   ![Előfizetés módosítása](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Válassza ki az áthelyezni kívánt erőforrásokat, itt azt javasoljuk, hogy az összes **kijelölése** lehetőséget választva válassza ki az összes felsorolt opcionális erőforrást.

   ![erőforrás áthelyezése](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Válassza ki a cél előfizetést az **előfizetés** legördülő listából, ahol a tárolót át szeretné helyezni.
6. A cél erőforráscsoport hozzáadásához az **erőforráscsoport** legördülő listában válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új csoport létrehozása** lehetőséget.

   ![Előfizetés hozzáadása](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Válassza a **megértettem, hogy az áthelyezett erőforrásokhoz társított eszközök és parancsfájlok nem fognak működni, amíg nem frissítem őket az új erőforrás-azonosítók** beállítás megadásához, majd válassza **az OK**gombot.

> [!NOTE]
> A több előfizetés biztonsági mentése (az RS-tároló és a védett virtuális gépek különböző előfizetésekben találhatók) nem támogatott forgatókönyv. Emellett a helyi redundáns tároló (LRS) és a globális redundáns tárterület (GRS) közötti tárterület-redundancia lehetősége nem módosítható a tár áthelyezési művelete során.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Recovery Services-tároló áthelyezése a PowerShell használatával

Recovery Services-tároló másik erőforráscsoporthoz való áthelyezéséhez használja a `Move-AzureRMResource` parancsmagot. `Move-AzureRMResource` az erőforrás nevét és típusát igényli. Mindkettőt lekérheti a `Get-AzureRmRecoveryServicesVault` parancsmagból.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Az erőforrások másik előfizetésbe való áthelyezéséhez adja meg a `-DestinationSubscriptionId` paramétert.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

A fenti parancsmagok végrehajtása után a rendszer felszólítja, hogy erősítse meg, hogy a megadott erőforrásokat át kívánja helyezni. A megerősítéshez írja be az **Y** értéket. Sikeres érvényesítés után az erőforrás áthelyeződik.

## <a name="use-cli-to-move-recovery-services-vault"></a>Recovery Services-tároló áthelyezése a CLI használatával

Recovery Services-tároló másik erőforráscsoporthoz való áthelyezéséhez használja a következő parancsmagot:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Új előfizetésre való áttéréshez adja meg a `--destination-subscription-id` paramétert.

## <a name="post-migration"></a>Migráció utáni feladatok

1. Az erőforráscsoportok hozzáférés-vezérlésének beállítása/ellenőrzése.  
2. A Mentés befejezését követően újra kell konfigurálni a biztonsági mentési jelentéskészítési és figyelési funkciót. Az áthelyezési művelet során az előző konfiguráció el fog veszni.

## <a name="next-steps"></a>További lépések

Több különböző típusú erőforrást is áthelyezhet az erőforráscsoportok és az előfizetések között.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).
