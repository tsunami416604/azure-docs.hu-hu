---
title: Frissítés az Azure virtuális gép biztonsági mentési verem V2 |} Microsoft Docs
description: Virtuális gép biztonsági mentési csomagjának V2 frissítése-folyamat és a gyakori kérdések
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 6d214072bccb8b2b42828ee003dcf349985b4f43
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Frissítés a virtuális gép biztonságimásolat-vermének 2-es verziójára
A virtuális gép (VM) biztonsági mentési verem V2 frissítése a következő továbbfejlesztett szolgáltatásokat nyújtja:
* Lásd: az adatátvitel végrehajtásához várakozás nélkül a helyreállítás elérhető legyen a biztonsági mentési feladat részeként készített pillanatképet lehetősége.
A visszaállítás elindítása előtt tároló másolandó pillanatkép várakozási csökkenti. Emellett ez kizárja a további tárhely követelmény kivételével első biztonsági mentés a prémium szintű virtuális gépek biztonsági mentéséről.  

* Biztonsági mentés és visszaállítás alkalommal megtartják a pillanatképek helyi 7 napig csökkentése. 

* Támogatja a lemez méretének akár 4 TB.  

* Képesek használni az eredeti storage-fiókok (akkor is, ha a virtuális gép lemezzel rendelkezik, amelyet elosztott storage-fiókok) mikor történt a visszaállítás, egy nem felügyelt virtuális Gépre. Ezzel biztosítható visszaállítások gyorsabban számos különböző Virtuálisgép-konfigurációk. 
    > [!NOTE] 
    > Ez nem ugyanaz, mint az eredeti virtuális felülbírálása. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>Mi az új verem változik?
A biztonsági mentési feladat jelenleg két fázisból áll:
1.  Virtuális gép pillanatkép készítéséhez. 
2.  Virtuális gép pillanatkép átvitele Azure Backup-tárolóban. 

A helyreállítási pont tekinthető létre csak azt követően 1 és 2 fázisban történik. Az új verem részeként a helyreállítási pont jön létre, amint a pillanatkép befejezése. A helyreállítási pont, az adott visszaállítási adatfolyam használatával is visszaállíthatja. A helyreállítási pont, az Azure portálon "pillanatkép" helyreállítási pont típusként azonosíthatja. Ha a pillanatkép átkerül a tároló, helyreállítási pont típus "Pillanatkép és tároló" módosításait. 

![A virtuális gép biztonsági mentési csomagjának V2 biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-flow.jpg) 

Alapértelmezés szerint hét napos pillanatképek lesznek megőrizve. Ez lehetővé teszi a visszaállítást végrehajtani gyorsabban ezeket a pillanatképeket az adatok másolása a tárolóból vissza az ügyfél tárfiókja szükséges idő csökkentése révén. 

## <a name="considerations-before-upgrade"></a>Frissítés előtt kapcsolatos szempontok
* Ez az a virtuális gép biztonsági mentési csomagjának egy egyirányú frissítését. Igen Ez a folyamat minden jövőbeni biztonsági mentés váltanak. Mivel a **engedélyezve van egy előfizetési szinten, minden virtuális gép kerül, ez a folyamat**. Az összes új funkciót elemek ugyanazt a vermet alapul. Ez a házirend szinten későbbi hamarosan vezérlése kiadását. 
* Virtuális gépek a premium lemezek esetében az első biztonsági mentés során győződjön meg arról, hogy a virtuális gép mérete megfelelő tárolóhely érhető el a tárfiókban lévő első biztonsági mentés befejeződéséig. 
* Pillanatképek helyileg tárolja a helyreállítási pont létrehozása növelése érdekében és visszaállítási felgyorsítása érdekében, mivel látni fogja a 7 napos időszak alatt pillanatképek megfelelő tárolási költségeket.
* Akkor használatos, ha a visszaállítás pillanatkép helyreállítási pont a prémium szintű virtuális gépek, látni fogja a virtuális gép létrehozása részeként a visszaállítása közben használt ideiglenes tárolási helye. 

## <a name="how-to-upgrade"></a>Hogyan lehet frissíteni?
### <a name="the-azure-portal"></a>Az Azure-portálon
Ha használja az Azure portál használatával, meg fog megjelenik egy értesítés a nagy lemeztámogatás kapcsolódó tároló irányítópult és biztonsági mentése és visszaállítása sebesség javítása.

![A virtuális gép biztonsági mentési csomagjának V2 biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-banner.png) 

Nyissa meg az új verem történő frissítés képernyő, jelölje be a szalagcím. 

![A virtuális gép biztonsági mentési csomagjának V2 biztonsági mentési feladat](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
A következő parancsmagokat egy emelt szintű PowerShell terminál végre:
1.  Jelentkezzen be az Azure-fiókjával. 

```
PS C:> Login-AzureRmAccount
```

2.  Válassza ki az előfizetést, az előzetes regisztrálni kívánt:

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Az előfizetés regisztrálása a private Preview verziójára:

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Ellenőrizze, hogy a frissítés befejezése
Egy rendszergazda jogú PowerShell terminálról futtassa az alábbi parancsmagot:

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Ha regisztrált felirat látható, az előfizetés virtuális gép biztonsági mentési csomagjának V2 legyen frissítve. 



