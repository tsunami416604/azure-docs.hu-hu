---
title: Azure-az Azure-bA vész-helyreállítási a mobilitási szolgáltatás automatikus frissítése |} Microsoft Docs
description: Automatikus frissítés a mobilitási szolgáltatás, amely az Azure virtuális gépek az Azure Site Recovery segítségével replikációs áttekintése.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: rajanaki
ms.openlocfilehash: 45f2e2927f699769bb385038c04d4dd23e075a9a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="automatic-update-of-mobility-service-extension-in-azure-to-azure-replication"></a>Azure-az Azure-bA replikációs a mobilitási szolgáltatás bővítmény automatikus frissítése

Az Azure Site Recovery rendelkezik egy havi kiadás ütemben történik, ha az a meglévő funkciók fejlesztéseivel, illetve újakat hozzáadásakor és az esetleges ismert probléma elhárítása. Ez azt jelenti, hogy maradjon naprakész lesz a szolgáltatás, meg kell terveznie a javítások telepítését havi ütemben történik. A frissítés társított over head elkerülése érdekében felhasználók választhatja kezelheti a frissítéseket az összetevők helyreállítás engedélyezéséhez. A részletes a [architektúra hivatkozás](azure-to-azure-architecture.md) Azure az Azure-bA vész-helyreállítási mobilitási szolgáltatás telepíti az összes Azure virtuális gépeken, amelynek a replikáció engedélyezve van egy Azure virtuális gépek replikálásához közben másik régióhoz. Ez a dokumentum a következő adatokat:

- Hogyan működik az automatikus frissítés?
- Az automatikus frissítések engedélyezése
- Gyakori problémák és hibaelhárítás
 
## <a name="how-does-automatic-update-work"></a>Hogyan működik az automatikus frissítés

Ha engedélyezi a Site Recovery kezelheti a frissítéseket, (által használt Azure-szolgáltatások) globális runbook automation-fiók, és a tárolónak ugyanahhoz az előfizetéshez létrehozott keresztül van telepítve. Egy automation-fiók egy adott tárolóban szolgál. A runbook ellenőrzi az egyes virtuális gépek az adott tárolóban, amelynek az automatikus frissítések ON vannak kapcsolva, és kezdeményezi a mobilitási szolgáltatás bővítmény frissítését, ha egy újabb verziója érhető el. A runbook recurrs naponta 12:00 órakor a replikált virtuális gép földrajzi időzónája szerint alapértelmezés szerinti ütemezését. A runbook-ütemezés is módosíthatja az automation-fiók használatával a felhasználó, ha szükséges. 

## <a name="enable-automatic-updates"></a>Az automatikus frissítések engedélyezése

Dönthet úgy, a következő módokon kezelheti a frissítéseket a hely helyreállítását lehetővé tevő:-

- [A replikáció engedélyezése lépésben](#as-part-of-the-enable-replication-step)
- [Váltás a bővítmény belül a tároló-beállítások frissítése](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Az engedélyezés replikálási lépést a részeként:

Engedélyezésekor replikációs vagy elindítása egy virtuális gépre vonatkozó [a virtuális gép nézetből](azure-to-azure-quickstart.md), vagy [a recovery services-tároló a](azure-to-azure-how-to-enable-replication.md), vagy engedélyezheti a Site Recovery számára, hogyan szeretné elérhetővé válik a Site Recovery-bővítmény vagy kézi kezelését azonos frissítések kezelése.

![Enable-replikáció – az automatikus frissítés](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Váltás a bővítmény belül a tároló-beállítások frissítése

1. A tároló belső navigáljon **kezelése**-> **Site Recovery-infrastruktúra**
2. A **az Azure virtuális gépek**-> **bővítmény frissítési beállítások**, kattintással válassza ki, hogy engedélyezi *kezelheti a frissítéseket az ASR* vagy *manuális kezelése*. Kattintson a **Save** (Mentés) gombra.

![tároló-váltógomb-autuo-frissítés](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Ha úgy dönt, *engedélyezése az ASR kezeléséhez*, a beállítást alkalmazza a megfelelő tárolóban lévő összes virtuális gépet.


> [!Note] 
> A mindkét lehetőség értesíti Önt az automation-fiók, amely a frissítések kezelésére szolgál. Ha engedélyezi ezt a szolgáltatást először az adott tárolóban, egy új automation-fiók jön létre. Minden ezt követő engedélyezése replikációk ugyanabban a tárolóban a korábban létrehozott egy fogja használni.

## <a name="common-issues--troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha az automatikus frissítések problémát, kell értesítést ugyanezen a tároló irányítópult konfigurációs problémák alatt. 

Próbált automatikus frissítés engedélyezése, és nem sikerült, tekintse meg alább a hibaelhárításhoz.

**Hiba**: nincs Azure-beli futtató fiók (egyszerű szolgáltatásnév) létrehozása és engedélyezése a közreműködő szerepkört az egyszerű szolgáltatás engedélyeit. 
- Javasolt művelet: Gondoskodjon arról, hogy a bejelentkezett fiók van hozzárendelve a "közreműködői", majd próbálja megismételni a műveletet.
 
Ha az automatikus frissítések ON vannak kapcsolva, a problémák nagy része is lehet sérülések, a Site Recovery szolgáltatás, és megköveteli, hogy az a "**javítási**" gombra.

![javítás-gomb](./media/azure-to-azure-autoupdate/repair.png)

Abban az esetben, ha a javítás gomb nem érhető el, tekintse át a bővítmény beállítások ablaktáblában jelenik meg hibaüzenet.

 - **Hiba**: A futtató fióknak nincs engedélye a helyreállítási szolgáltatások erőforrás elérésére.

    **Javasolt művelet**: törölje, majd [hozza létre a futtató fiók](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) , vagy győződjön meg arról, hogy az Automation Futtatás mint fiók Azure Active Directory-alkalmazást a recovery services erőforrás elérésére.

- **Hiba**: nem található a futtató fiók. Az egyik törölték vagy létre sem hozták - Azure Active Directory-alkalmazást, a szolgáltatás egyszerű, a szerepkör, vagy Automation szolgáltatásbeli tanúsítványeszköz, Automation szolgáltatásbeli kapcsolódási eszköz - vagy az ujjlenyomat nincs azonos tanúsítványt és a kapcsolat között. 

    **Javasolt művelet**: törölje és [hozza létre a futtató fiók](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
