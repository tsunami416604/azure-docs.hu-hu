---
title: Helyezze át egy Recovery Services-tárolót az Azure-előfizetések között, vagy egy másik erőforráscsoportba
description: Utasításokat követve helyezze át a recovery services-tároló az azure-előfizetések és -erőforráscsoportok között.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2019
ms.author: sogup
ms.openlocfilehash: 0eb19ba8278df2d77466e5be13731723557e85a8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082075"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Recovery Services-tároló áthelyezése az Azure-előfizetések és erőforráscsoportok (korlátozott nyilvános előzetes verzió)

Ez a cikk bemutatja, hogyan helyezheti át a több Azure-előfizetés, vagy egy másik erőforráscsoportba ugyanabban az előfizetésben az Azure Backup konfigurálása a Recovery Services-tároló. Az Azure portal vagy a PowerShell segítségével helyezze át egy Recovery Services-tárolót.

## <a name="prerequisites-for-moving-a-vault"></a>Tároló áthelyezése előfeltételei

- Erőforráscsoportok közötti áthelyezésekor a forrásként szolgáló erőforráscsoportot és a célként megadott erőforráscsoportja zárolva vannak a művelet során. Mindaddig, amíg befejeződik az áthelyezés, írási és törlési műveletek erőforráscsoportokról le vannak tiltva.
- Az előfizetés-rendszergazda számára a helyezni egy tárolót.
- Tároló áthelyezése előfizetések között, amikor a célként megadott előfizetés engedélyezett állapotban léteznie kell, és ugyanabban a bérlőben, mint a forrás-előfizetés kell lennie.
- Írási műveletek végrehajtása a célként megadott erőforráscsoportja engedéllyel kell rendelkeznie.
- A Recovery Services-tároló helye nem módosítható. Az erőforráscsoport csak a tár áthelyezése módosítja. Az új erőforráscsoport lehet egy másik helyen található, de a tároló helye, amely nem változik.
- Jelenleg áthelyezheti egy Recovery Services-tárolót, régiónként, egyszerre.
- Ha egy virtuális gép nem helyezi át a helyreállítási tárban, előfizetések között, vagy egy új erőforráscsoportot, a virtuális gép aktuális helyreállítási pontok változatlanok maradnak a tárolóban amíg le nem járnak.
- A virtuális gép áthelyezése a tárolóval vagy sem, hogy mindig visszaállíthatja a virtuális gép a megőrzött korábbi biztonsági másolatok a tárolóban.
-   Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek található az ugyanazon Azure-régióban és az előfizetés.
-   A felügyelt lemezekkel rendelkező virtuális gép áthelyezése: Ez [cikk](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
-   A beállításokat a klasszikus modellben telepített erőforrások áthelyezéséhez eltér attól függően, hogy helyez át az erőforrásokat egy előfizetésen belül, vagy egy új előfizetést. További információkért lásd: Ez [cikk](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
-   A tároló számára meghatározott biztonsági mentési szabályzatok megmaradnak, miután a tároló áthelyezése előfizetések között, vagy egy új erőforráscsoportot.
-   Jelenleg nem helyezhetők át a tárolók az Azure Files, az Azure File Sync vagy az SQL IaaS-beli virtuális gépeken tartalmazó különböző előfizetésekhez és erőforráscsoportokhoz. Ezek a forgatókönyvek támogatása bekerül a jövőbeli kiadások.
-   Ha egy tárolót tartalmazó virtuális gép biztonsági mentési adatokat, előfizetések, helyezze át a virtuális gépek ugyanahhoz az előfizetéshez, és az biztonsági mentések továbbra is az azonos céloldali erőforráscsoport segítségével.<br>

> [!NOTE]
> 
> Recovery Services-tárolók használata konfigurált **Azure Site Recovery** nem helyezhető át, még. Ha olyan virtuális gépek konfigurálása (Azure IaaS, Hyper-V, VMware) vagy a vész-helyreállítási a fizikai gépek a **Azure Site Recovery**, az áthelyezési művelet le lesz tiltva. A Site Recovery szolgáltatáshoz erőforrás áthelyezési funkció még nem érhető el.

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>A helyreállítási tár áthelyezése a forrás-előfizetés regisztrálása

A forrás-előfizetés regisztrálása **áthelyezése** a Recovery Services-tárolót, a következő parancsmagok futtatásához a PowerShell terminálról:

1. Jelentkezzen be az Azure-fiókjába

   ```
   Connect-AzureRmAccount
   ```

2. Válassza ki a regisztrálni kívánt előfizetést

   ```
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```
3. Ez az előfizetés regisztrálása

   ```
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. A parancs futtatása

   ```
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

Várjon 30 perc alatt az előfizetést engedélyezési listára kell az áthelyezési művelet az Azure portal vagy a PowerShell használatával az megkezdése előtt.

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Recovery Services-tároló áthelyezése másik erőforráscsoportba az Azure portal használatával

A recovery services-tároló és az összes kapcsolódó erőforrás, másik erőforráscsoportba áthelyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a listáját **Recovery Services-tárolók** , és válassza ki az áthelyezni kívánt tárolót. A tároló irányítópultjának megnyitása után megjelenik az alábbi képen látható módon.

   ![Nyissa meg a helyreállítás Services-tároló](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Ha nem látja a **Essentials** információk tárolót, kattintson a legördülő ikonra. Most látnia kell az Essentials adatait a tároló számára.

   ![Essentials információs lapon](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintés menüben kattintson a **módosítása** mellett a **erőforráscsoport**, megnyitásához a **erőforrások áthelyezése** panelen.

   ![Váltás másik erőforráscsoportra](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Az a **erőforrások áthelyezése** panelen a kiválasztott kulcstartó esetében javasoljuk, hogy a választható kapcsolódó erőforrások áthelyezéséhez jelölje be az alábbi képen látható módon.

   ![Helyezze át az előfizetést](./media/backup-azure-move-recovery-services/move-resource.png)

5. A célerőforrás-csoport hozzáadása a a **erőforráscsoport** legördülő listában válassza ki valamelyik meglévő erőforrására csoporthoz, vagy kattintson a **hozzon létre egy új csoportot** lehetőséget.

   ![Erőforrás létrehozása](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Miután hozzáadta az erőforráscsoport, erősítse meg **megértettem, hogy az áthelyezett erőforrásokhoz kapcsolódó eszközök és szkriptek nem fog működni amíg nem frissítem azokat az új erőforrás-azonosítók használata** lehetőséget, majd kattintson **OK** végrehajtásához a tár áthelyezését.

   ![Megerősítő üzenet](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Recovery Services-tároló áthelyezése egy másik előfizetésben az Azure portal használatával

Recovery Services-tároló és az összes kapcsolódó erőforrás áthelyezheti egy másik előfizetésre

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a Recovery Services-tárolók listájában, és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultjának, megjelenik az alábbi képen látható módon.

    ![Nyissa meg a helyreállítás Services-tároló](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Ha nem látja a **Essentials** információk tárolót, kattintson a legördülő ikonra. Most látnia kell az Essentials adatait a tároló számára.

    ![Essentials információs lapon](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintés menüben kattintson a **módosítása** melletti **előfizetés**, megnyitásához a **erőforrások áthelyezése** panelen.

   ![Váltás másik előfizetésre](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Válassza ki az erőforrásokat áthelyezni, itt javasoljuk, hogy használja a **válassza ki az összes** beállítást válassza ki az összes felsorolt választható erőforrásokat.

   ![erőforrások áthelyezése](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Válassza ki a cél előfizetést a **előfizetés** legördülő listából válassza ki, hol szeretne a tárolóhoz lesz áthelyezve.
6. A célerőforrás-csoport hozzáadása a a **erőforráscsoport** legördülő listában válassza ki valamelyik meglévő erőforrására csoporthoz, vagy kattintson a **hozzon létre egy új csoportot** lehetőséget.

   ![Előfizetés hozzáadása](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Kattintson a **megértettem, hogy az áthelyezett erőforrásokhoz kapcsolódó eszközök és szkriptek nem fog működni amíg nem frissítem azokat az új erőforrás-azonosítók használata** erősítse meg, és kattintson a beállítással **OK**.

> [!NOTE]
> Előfizetés biztonsági mentés közötti (RS tároló és a védett virtuális gépek különböző előfizetésekhez tartoznak) nem támogatja. Ezenkívül tárhely-redundancia globális redundáns tárolást (GRS) a helyi zónaredundáns tárolás (LRS) lehetőséget, és fordítva nem lehet módosítani a tároló áthelyezési művelet során.
>
>

## <a name="use-powershell-to-move-a-vault"></a>Egy tároló áthelyezéséhez a PowerShell használatával

Recovery Services-tároló áthelyezése másik erőforráscsoportba, használja a `Move-AzureRMResource` parancsmagot. `Move-AzureRMResource` az erőforrás nevét és a felhasznált erőforrás típusa, igényel. A is beszerezheti a `Get-AzureRmRecoveryServicesVault` parancsmagot.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Az erőforrások áthelyezése másik előfizetésben, adjon meg a `-DestinationSubscriptionId` paraméter.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

A fenti parancsmagok végrehajtása után kell adnia annak ellenőrzéséhez, hogy szeretné-e a megadott erőforrások áthelyezése. Típus **Y** megerősítéséhez. Sikeres ellenőrzést követően az erőforrás áthelyezése.

## <a name="use-cli-to-move-a-vault"></a>Tároló áthelyezése a CLI használatával

Recovery Services-tároló áthelyezése másik erőforráscsoportba, használja a következő parancsmagot:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Szeretne áthelyezni egy új előfizetést, adja meg a `--destination-subscription-id` paraméter.

## <a name="post-migration"></a>A migrálás után

1. Az erőforráscsoportok hozzáférés-vezérlés beállítása/ellenőriznie kell.  
2. A biztonsági mentés jelentéskészítés és a figyelési funkció kell újra a tár bejegyzés az Áthelyezés befejezése legyen beállítva. A korábbi konfiguráció az áthelyezési művelet során elvesznek.



## <a name="next-steps"></a>További lépések

Számos különböző típusú erőforrások helyezheti át erőforráscsoportok és előfizetések között.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
