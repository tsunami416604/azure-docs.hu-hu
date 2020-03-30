---
title: Az Azure Backup Recovery Services-tárolók áthelyezése
description: Útmutató a helyreállítási szolgáltatások tárolójának áthelyezéséhez az Azure-előfizetések és erőforráscsoportok között.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: fed42c578da2e4f27f42e11d5ac67d698bbcd939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120724"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Helyreállítási szolgáltatások tárolójának áthelyezése az Azure-előfizetések és erőforráscsoportok között

Ez a cikk bemutatja, hogyan helyezheti át az Azure Backup hoz konfigurált helyreállítási szolgáltatások trezorát az Azure-előfizetések között, vagy egy másik erőforráscsoportba ugyanabban az előfizetésben. Az Azure Portalon vagy a PowerShell segítségével áthelyezheti a Recovery Services-tárolót.

## <a name="supported-regions"></a>Támogatott régiók

Erőforrás move for Recovery Services boltozat támogatott Ausztrália Kelet- és Ausztrália Délkeleti, Kanada Közép-, Kanada Kelet-, Délkelet-Ázsia, Kelet-Ázsia, Usa középső régiója, USA északi középső régiója, USA keleti régiója, USA keleti régiója, USA délnyugati középső régiója, USA nyugati középső régiója, USA nyugati középső régiója, Közép-India, Dél-India, Japán Kelet- és Japán-, Közép-Korea, Dél-Korea, Észak-Európa, Nyugat-Európa, Dél-Afrika Észak-, Dél-Afrika Nyugat-, Egyesült Királyság Déli és Egyesült Királyság Nyugati ja.

## <a name="unsupported-regions"></a>Nem támogatott régiók

Franciaország közép, Franciaország Dél, Németország északkeleti, Németország Központi, US Gov Iowa, Kína Észak,Kína North2, Kína Kelet, Kína Kelet2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>A Helyreállítási szolgáltatások tárolójának áthelyezésének előfeltételei

- A tároló erőforráscsoportok közötti áthelyezése során mind a forrás, mind a célerőforrás-csoportok zárolva vannak, megakadályozva az írási és törlési műveleteket. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Csak a rendszergazdai előfizetés rendelkezik a tároló áthelyezéséhez szükséges engedélyekkel.
- A tároló előfizetések közötti áthelyezése, a cél-előfizetés nek ugyanabban a bérlőben kell lennie, mint a forrás-előfizetés, és az állapotát engedélyezni kell.
- A célerőforrás-csoport írási műveleteinek végrehajtásához engedéllyel kell rendelkeznie.
- A tároló áthelyezése csak az erőforráscsoportot módosítja. A Recovery Services-tároló ugyanazon a helyen található, és nem módosítható.
- Régiónként és régiónként csak egy helyreállítási szolgáltatás-tárolót helyezhet át.
- Ha egy virtuális gép nem mozog a Recovery Services-tároló előfizetések között, vagy egy új erőforráscsoport, az aktuális virtuálisgép helyreállítási pontok érintetlenmaradnak a tárolóban, amíg le nem járnak.
- Függetlenül attól, hogy a virtuális gép a tárolóval együtt kerül-e áthelyezésre, bármikor visszaállíthatja a virtuális gép a tárolóban megőrzött biztonsági mentési előzményekből.
- Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek ugyanabban az Azure-régióban és előfizetésben található.
- A felügyelt lemezekkel rendelkező virtuális gépek áthelyezéséről ebben a [cikkben](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)olvashat.
- A klasszikus modellen keresztül üzembe helyezett erőforrások áthelyezésének lehetőségei attól függően változnak, hogy egy előfizetésen belül vagy egy új előfizetésen belül helyezi-e át az erőforrásokat. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- A tárolóhoz definiált biztonsági mentési szabályzatok megmaradnak, miután a tároló előfizetések között vagy egy új erőforráscsoportba kerül.
- Az Azure Files, az Azure File Sync vagy az SQL áthelyezése az IaaS virtuális gépeken előfizetések és erőforráscsoportok között nem támogatott.
- Ha áthelyez egy virtuális gép biztonsági mentési adatait tartalmazó tárolót, előfizetések között, át kell helyeznie a virtuális gépeket ugyanabba az előfizetésbe, és ugyanazt a cél virtuálisgép-erőforráscsoport nevét (ahogy a régi előfizetésben) kell használnia a biztonsági mentések folytatásához.

> [!NOTE]
>
> Az **Azure Site Recovery** használatával konfigurált helyreállítási szolgáltatások tárolói még nem tudnak áthelyezni. Ha konfigurált a virtuális gépek (Azure IaaS, Hyper-V, VMware) vagy a fizikai gépek vész-helyreállítási az **Azure Site Recovery**használatával, az áthelyezési művelet blokkolva lesz. A Site Recovery szolgáltatás erőforrás-áthelyezési szolgáltatása még nem érhető el.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Az Azure Portal használata a Recovery Services-tároló áthelyezése különböző erőforráscsoportokba

Helyreállítási szolgáltatások tárolójának és a hozzá tartozó erőforrásoknak a másik erőforráscsoportba való áthelyezése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Nyissa meg a **Helyreállítási szolgáltatások tárolóinak** listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor a tároló irányítópultja megnyílik, az alábbi képen látható módon jelenik meg.

   ![Szolgáltatástároló helyreállítása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Ha nem látja a trezor **Essentials** adatait, kattintson a legördülő ikonra. Most látnia kell a trezor Essentials-adatait.

   ![Az Essentials Information lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintése menüben kattintson a **Módosítás** gombra az **Erőforrás csoport**mellett az **Erőforrások áthelyezése** panel megnyitásához.

   ![Erőforráscsoport módosítása](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Az **Erőforrások áthelyezése** panelen a kijelölt tárolóhoz ajánlott a választható kapcsolódó erőforrások áthelyezése a jelölőnégyzet bejelölésével, ahogy az az alábbi képen látható.

   ![Előfizetés áthelyezése](./media/backup-azure-move-recovery-services/move-resource.png)

5. A célerőforrás-csoport hozzáadásához az **Erőforráscsoport** legördülő listában jelöljön ki egy meglévő erőforráscsoportot, vagy kattintson **az Új csoport létrehozása** beállításra.

   ![Erőforrás létrehozása](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Az erőforráscsoport hozzáadása után győződjön meg arról, **hogy megértettem, hogy az áthelyezett erőforrásokhoz társított eszközök és parancsfájlok nem fognak működni, amíg nem frissítem őket az új erőforrás-azonosítók használatához,** majd kattintson az **OK** gombra a tároló áthelyezésének befejezéséhez.

   ![Megerősítő üzenet](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>A Recovery Services-tároló áthelyezése egy másik előfizetésbe az Azure Portalon

A Recovery Services-tárolót és a hozzá tartozó erőforrásokat áthelyezheti egy másik előfizetésbe

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Nyissa meg a Helyreállítási szolgáltatások tárolóinak listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor a tároló irányítópultja megnyílik, az alábbi képen látható módon jelenik meg.

    ![Szolgáltatástároló helyreállítása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Ha nem látja a trezor **Essentials** adatait, kattintson a legördülő ikonra. Most látnia kell a trezor Essentials-adatait.

    ![Az Essentials Information lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A trezor áttekintése menüben kattintson a **Módosítás** gombra az **Előfizetés**elem mellett az **Erőforrások áthelyezése** panel megnyitásához.

   ![Előfizetés módosítása](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Válassza ki az áthelyezendő erőforrásokat, itt azt javasoljuk, hogy az **Összes kijelölése** beállítással jelölje ki az összes felsorolt választható erőforrást.

   ![erőforrás áthelyezése](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Válassza ki a cél-előfizetést az **Előfizetés** legördülő listából, ahol át szeretné helyezni a tárolót.
6. A célerőforrás-csoport hozzáadásához az **Erőforráscsoport** legördülő listában jelöljön ki egy meglévő erőforráscsoportot, vagy kattintson **az Új csoport létrehozása** beállításra.

   ![Előfizetés hozzáadása](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Kattintson **a Megértem, hogy az áthelyezett erőforrásokhoz társított eszközök és parancsfájlok nem fognak működni, amíg nem frissítem őket az új erőforrásazonosítók használatához** a megerősítéshez, majd kattintson az **OK**gombra.

> [!NOTE]
> A keresztelőfizetéses biztonsági mentés (az RS-tároló és a védett virtuális gépek különböző előfizetésekben vannak) nem támogatott forgatókönyv. Emellett a tárolási redundancia lehetőség a helyi redundáns tárolás (LRS) a globális redundáns tárolás (GRS) és fordítva nem módosítható a tároló áthelyezési művelet során.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>A Recovery Services-tároló áthelyezése a PowerShell használatával

A Recovery Services-tároló áthelyezése egy `Move-AzureRMResource` másik erőforráscsoportba, használja a parancsmag. `Move-AzureRMResource`az erőforrás nevét és típusát igényli. Mindkettőt megkaphatja `Get-AzureRmRecoveryServicesVault` a parancsmagból.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Az erőforrások másik előfizetésbe való `-DestinationSubscriptionId` áthelyezéséhez adja meg a paramétert.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

A fenti parancsmagok végrehajtása után meg kell erősítenie, hogy át kívánja helyezni a megadott erőforrásokat. Írja be az **Y** típust a megerősítéshez. Sikeres ellenőrzés után az erőforrás áthelyeződik.

## <a name="use-cli-to-move-recovery-services-vault"></a>A CLI használata a Helyreállítási szolgáltatások tárolójának áthelyezése

A Recovery Services-tároló másik erőforráscsoportba való áthelyezéséhez használja a következő parancsmast:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Új előfizetésre való áttéréshez `--destination-subscription-id` adja meg a paramétert.

## <a name="post-migration"></a>Migráció utáni feladatok

1. Az erőforráscsoportok hozzáférés-vezérlésének beállítása/ellenőrzése.  
2. A biztonsági mentésjelentési és figyelési funkciót újra be kell állítani a tárolóaz áthelyezés befejeződése esetén. Az előző konfiguráció elvész az áthelyezési művelet során.

## <a name="next-steps"></a>További lépések

Az erőforráscsoportok és az előfizetések között számos különböző típusú erőforrást helyezhet át.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
