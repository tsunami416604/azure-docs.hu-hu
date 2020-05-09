---
title: Azure Backup Recovery Services-tárolók áthelyezése
description: Útmutató a Recovery Services-tároló Azure-előfizetések és-erőforráscsoportok közötti áthelyezéséhez.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 93c3f2db6500023755796d50e71d44a427a2ce82
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597994"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Recovery Services-tároló áthelyezése az Azure-előfizetések és-erőforráscsoportok között

Ez a cikk azt ismerteti, hogyan helyezhet át egy Recovery Services-tárolót az Azure-előfizetések között Azure Backuphoz, vagy egy másik erőforráscsoporthoz ugyanabban az előfizetésben. Recovery Services-tároló áthelyezéséhez a Azure Portal vagy a PowerShellt használhatja.

## <a name="supported-regions"></a>Támogatott régiók

A Recovery Services-tár erőforrás-áthelyezését a Kelet-Ausztrália támogatja. Kelet-Ausztrália, Közép-Kanada, Kelet-Kanada, Dél-Kelet-Ázsia, Kelet-Ázsia, az USA középső régiója, az USA északi középső régiója, az USA keleti régiója, a keleti RÉGIÓJA, az USA déli középső régiója, az USA nyugati középső régiója, az USA nyugati középső régiója, Közép-India, Dél-India, Kelet-Japán, Nyugat-Korea , Dél-Afrika nyugati, Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója.

## <a name="unsupported-regions"></a>Nem támogatott régiók

Közép-Franciaország, Dél-Franciaország, Északkelet-Németország, Közép-Németország, US Gov Iowa, Észak-Kína, Kína North2, Kelet-Kína, Kína készletek

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Recovery Services-tároló áthelyezésének előfeltételei

- A tárolóban az erőforráscsoportok között a forrás-és a célként megadott erőforráscsoportok is zárolva vannak az írási és törlési műveletek megakadályozása érdekében. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Csak a rendszergazdai előfizetés rendelkezik a tároló áthelyezéséhez szükséges engedélyekkel.
- A tárolók előfizetések közötti áthelyezéséhez a cél előfizetésnek ugyanabban a bérlőben kell lennie, mint a forrás-előfizetésnek, és annak állapotát engedélyezni kell.
- A cél erőforráscsoporthoz írási műveletek végrehajtásához engedéllyel kell rendelkeznie.
- A tár áthelyezése csak az erőforráscsoportot módosítja. A Recovery Services-tároló ugyanazon a helyen fog megjelenni, és nem módosítható.
- Régiónként egyszerre csak egy Recovery Services-tárolót lehet áthelyezni.
- Ha egy virtuális gép nem a Recovery Services-tárolóval, vagy egy új erőforráscsoporthoz kerül át, akkor az aktuális virtuálisgép-helyreállítási pontok sértetlenek maradnak a tárolóban mindaddig, amíg lejárnak.
- Azt jelzi, hogy a virtuális gép a tárolóval van-e áthelyezve, vagy sem, bármikor visszaállíthatja a virtuális gépet a tár megőrzött biztonsági mentési előzményeiből.
- A Azure Disk Encryption megköveteli, hogy a Key Vault és a virtuális gépek ugyanabban az Azure-régióban és-előfizetésben legyenek tárolva.
- Ha felügyelt lemezekkel rendelkező virtuális gépet szeretne áthelyezni, tekintse meg ezt a [cikket](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- A klasszikus modellben üzembe helyezett erőforrások áthelyezésének lehetőségei eltérnek attól függően, hogy az adott előfizetésen belül vagy egy új előfizetésre helyezi át az erőforrásokat. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- A tárolóhoz definiált biztonsági mentési szabályzatok megmaradnak, miután a tár előfizetések vagy egy új erőforráscsoport között mozog.
- Csak akkor helyezhető át tároló, ha az Azure Virtual Machines csak az egyetlen biztonsági mentési elem a tárban.
- Ha egy virtuális gép biztonsági mentési adatait tartalmazó tárolót helyez át az előfizetések között, át kell helyeznie a virtuális gépeket ugyanahhoz az előfizetéshez, és ugyanazt a cél virtuálisgép-erőforráscsoport nevét kell használnia (mint a régi előfizetésben) a biztonsági mentések folytatásához.

> [!NOTE]
> Az Azure-régiók Azure Backup Recovery Services-tárolóinak áthelyezése nem támogatott.<br><br>
> Ha a **Azure site Recovery**használatával bármilyen virtuális gépet (Azure IaaS, Hyper-V, VMware) vagy fizikai gépet konfigurált, akkor az áthelyezési művelet le lesz tiltva. Ha Azure Site Recovery tárolóit szeretné áthelyezni, tekintse át [ezt a cikket](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) , és ismerkedjen meg a tárolók manuális mozgatásával.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Recovery Services-tár másik erőforráscsoporthoz való áthelyezéséhez használja a Azure Portal

A Recovery Services-tároló és a hozzá tartozó erőforrások áthelyezése más erőforráscsoport-csoportba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg **Recovery Services** -tárolók listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultja, az a következő képen látható módon jelenik meg.

   ![A Service Vault helyreállítási tárolójának megnyitása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Ha nem látja a tár **alapvető** információit, kattintson a legördülő ikonra. Ekkor látnia kell a tár alapvető információit.

   ![Alapvető információk lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintése menüben kattintson az **erőforráscsoport**melletti **módosítás** elemre az **erőforrások áthelyezése** panel megnyitásához.

   ![Erőforráscsoport módosítása](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Az **erőforrások áthelyezése** panelen a kiválasztott tároló esetében ajánlott áthelyezni a választható kapcsolódó erőforrásokat a jelölőnégyzet bejelölésével az alábbi ábrán látható módon.

   ![Előfizetés áthelyezése](./media/backup-azure-move-recovery-services/move-resource.png)

5. A cél erőforráscsoport hozzáadásához az **erőforráscsoport** legördülő listában válasszon ki egy meglévő erőforráscsoportot, vagy kattintson az **új csoport létrehozása** lehetőségre.

   ![Erőforrás létrehozása](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Az erőforráscsoport hozzáadása után erősítse meg, **hogy az áthelyezett erőforrásokhoz társított eszközök és parancsfájlok nem fognak működni, amíg nem frissítem őket az új erőforrás-azonosítók** lehetőségre, majd kattintson **az OK** gombra a tár áthelyezésének befejezéséhez.

   ![Megerősítő üzenet](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Recovery Services-tároló áthelyezése másik előfizetésre a Azure Portal használatával

Egy Recovery Services-tárolót és a hozzá tartozó erőforrásokat egy másik előfizetésbe helyezheti át

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg Recovery Services-tárolók listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultja, az a következő képen látható módon jelenik meg.

    ![A Service Vault helyreállítási tárolójának megnyitása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Ha nem látja a tár **alapvető** információit, kattintson a legördülő ikonra. Ekkor látnia kell a tár alapvető információit.

    ![Alapvető információk lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintése menüben kattintson az **előfizetés**elem melletti **módosítás** elemre az **erőforrások áthelyezése** panel megnyitásához.

   ![Előfizetés módosítása](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Válassza ki az áthelyezni kívánt erőforrásokat, itt azt javasoljuk, hogy az összes **kijelölése** lehetőséget választva válassza ki az összes felsorolt opcionális erőforrást.

   ![erőforrás áthelyezése](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Válassza ki a cél előfizetést az **előfizetés** legördülő listából, ahol a tárolót át szeretné helyezni.
6. A cél erőforráscsoport hozzáadásához az **erőforráscsoport** legördülő listában válasszon ki egy meglévő erőforráscsoportot, vagy kattintson az **új csoport létrehozása** lehetőségre.

   ![Előfizetés hozzáadása](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Kattintson a **megértettem, hogy az áthelyezett erőforrásokhoz társított eszközök és parancsfájlok nem fognak működni, amíg nem frissíti őket az új erőforrás-azonosítók** beállítás megadásához, majd kattintson **az OK**gombra.

> [!NOTE]
> A több előfizetés biztonsági mentése (az RS-tároló és a védett virtuális gépek különböző előfizetésekben találhatók) nem támogatott forgatókönyv. Emellett a helyi redundáns tároló (LRS) és a globális redundáns tárterület (GRS) közötti tárterület-redundancia lehetősége nem módosítható a tár áthelyezési művelete során.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Recovery Services-tároló áthelyezése a PowerShell használatával

Recovery Services-tároló másik erőforráscsoporthoz való áthelyezéséhez használja a `Move-AzureRMResource` parancsmagot. `Move-AzureRMResource`az erőforrás nevét és típusát igényli. Mindkettőt lekérheti a `Get-AzureRmRecoveryServicesVault` parancsmagból.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Az erőforrások másik előfizetésbe való áthelyezéséhez adja `-DestinationSubscriptionId` meg a paramétert.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

A fenti parancsmagok végrehajtása után a rendszer felszólítja, hogy erősítse meg, hogy a megadott erőforrásokat át kívánja helyezni. A megerősítéshez írja be az **Y** értéket. Sikeres érvényesítés után az erőforrás áthelyeződik.

## <a name="use-cli-to-move-recovery-services-vault"></a>Recovery Services-tároló áthelyezése a CLI használatával

Recovery Services-tároló másik erőforráscsoporthoz való áthelyezéséhez használja a következő parancsmagot:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Új előfizetésre való áttéréshez adja meg `--destination-subscription-id` a paramétert.

## <a name="post-migration"></a>Migráció utáni feladatok

1. Az erőforráscsoportok hozzáférés-vezérlésének beállítása/ellenőrzése.  
2. A biztonsági mentés jelentéskészítési és figyelési funkcióját újra be kell állítani ahhoz, hogy a tár végrehajtsa az áthelyezést. Az áthelyezési művelet során az előző konfiguráció el fog veszni.

## <a name="next-steps"></a>További lépések

Több különböző típusú erőforrást is áthelyezhet az erőforráscsoportok és az előfizetések között.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
