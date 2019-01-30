---
title: A mobilitási szolgáltatás automatikus frissítése az Azure-bA vész-helyreállítási |} A Microsoft Docs
description: A mobilitási szolgáltatás automatikus frissítése áttekintést nyújt, ha az Azure Site Recovery használatával az Azure virtuális gépek replikálása.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: f31fccd2bf6d0daae03b025b53a41a0fad4ce2ef
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210131"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Az Azure-bA replikálása a mobilitási szolgáltatás automatikus frissítése

Az Azure Site Recovery egy havi tempót, ahol hozzá meglévő funkciók fejlesztéseivel, vagy újakat ad, és az esetleges ismert probléma elhárítása rendelkezik. Ez azt jelentené, hogy továbbra is a szolgáltatás naprakész, meg kell terveznie a javítások havi központi telepítésére vonatkozóan. Annak érdekében, hogy a frissítés társított over vezetője, felhasználók ehelyett eldönthetik, hogy a Site Recovery összetevőinek frissítéseinek kezelésére. A részletes a [architektúra referencia](azure-to-azure-architecture.md) vészhelyreállítás Azure-ba, a mobilitási szolgáltatást telepíti, amelynek a replikáció engedélyezve van egy Azure virtuális gépek replikálása során minden Azure virtuális gépeken -régióból a másikba. Miután engedélyezte az automatikus frissítés, a mobilitási szolgáltatás bővítmény frissül, és minden egyes új kiadással. Ez a dokumentum részletesen a következőket:

- Hogyan működik az automatikus frissítési?
- Automatikus frissítések engedélyezése
- Gyakori problémák és hibaelhárítás
 
## <a name="how-does-automatic-update-work"></a>Hogyan működik az automatikus frissítési

Ha engedélyezi a Site Recovery kezelheti a frissítéseket, globális runbookot (ami Azure-szolgáltatások által használt) egy automation-fiókot, és a tárolónak ugyanabban az előfizetésben létrehozott keresztül van telepítve. Egy adott tároló egy automation-fiók szolgál. A runbook minden virtuális gép automatikusan frissül, amelyhez be vannak kapcsolva tárolóban keres, és kezdeményezi a mobilitási szolgáltatás bővítmény frissítését, ha egy újabb verzió érhető el. A forgatókönyv az alapértelmezett ütemezés szerint naponta 12:00 órakor a replikált virtuális gép földrajzi időzónájának megfelelően továbbra is megjelenik. A runbook-ütemezés is módosítható az automation-fiók segítségével a felhasználó által szükség esetén. 

> [!NOTE]
> Az automatikus frissítések engedélyezése az Azure-beli virtuális gépek újraindítása nem szükséges, és nem befolyásolja a folyamatban lévő replikációkat.

> [!NOTE]
> Automation-fiókot használja a feladatok díjszabása a feladat futási ideje a hónap és alapértelmezés szerint 500 perc alatt felhasznált percek is tartozó automation-fiók ingyenes egységek számát alapul. A feladat napi összegeket a végrehajtása egy **néhány másodperc alatt körülbelül egy percig** és lesz **szereplő az ingyenes krediteket**.

Az ingyenes egységek (HAVONTA) foglalt ** ár feladat futtatása idő 500 perc ₹0.14 / perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

Dönthet úgy, hogy engedélyezze a következő módokon kezelheti a frissítéseket a Site Recovery:-

- [A replikáció engedélyezése lépés részeként](#as-part-of-the-enable-replication-step)
- [Váltás a bővítményt a táron belüli beállításainak frissítése](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>A replikáció engedélyezése lépés részeként:

Ha engedélyezi a replikációt a egy virtuális gép elindítása vagy [a virtuálisgép-nézetből](azure-to-azure-quickstart.md), vagy [a recovery services-tárolót a](azure-to-azure-how-to-enable-replication.md), kap, vagy engedélyezheti a Site Recovery az lehetőség a Site Recovery-bővítmény vagy kézi kezelését azonos frissítéseinek kezelése.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Váltás a bővítményt a táron belüli beállításainak frissítése

1. Lépjen a táron belüli **kezelés**-> **Site Recovery-infrastruktúra**
2. Alatt **Azure-beli virtuális gépek**-> **bővítmény frissítési beállítások**, kattintással válassza ki, hogy szeretné-e engedélyezése *kezelheti a frissítéseket az ASR* vagy *manuális kezelése*. Kattintson a **Save** (Mentés) gombra.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Ha úgy dönt *lehetővé teszik az ASR kezeléséhez*, a beállítást alkalmazza a megfelelő tárolóban lévő összes virtuális gépet.


> [!Note] 
> A két lehetőség értesíteni fogjuk, az automation-fiók, amely a frissítések kezelésére szolgál. Ha egy tárolót az első alkalommal engedélyezi ezt a szolgáltatást, létrejön egy új automation-fiókot. Minden ezt követő engedélyezése replikációk ugyanahhoz a tárolóhoz a korábban létrehozott fogja használni.

### <a name="manage-manually"></a>Manuális kezelés

1. Ha vannak a mobilitási szolgáltatás az Azure virtuális gépeken telepített új frissítéseket, megjelenik egy értesítés, olvasó, "új Site recovery replikációs ügynökének frissítése érhető el. Telepítéséhez kattintson ide."

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Válassza ki az értesítés a virtuális gép kiválasztására szolgáló lap megnyitásához.
4. Válassza ki a futó mobilitási szolgáltatás frissítése, és válassza ki a kívánt virtuális gépeket **OK**.

     ![Replikált elemek Virtuálisgép-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)

A mobilitási szolgáltatás frissítése feladat elindul, a kiválasztott virtuális gépek mindegyikéhez.


## <a name="common-issues--troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha az automatikus frissítések problémáját, értesítést is ugyanannak a tároló irányítópultján konfigurációs problémákat. 

Abban az esetben próbálta meg engedélyezni az automatikus frissítések, és nem sikerült, tekintse meg alább a hibaelhárításhoz.

**Hiba**: Nem jogosult Azure-beli futtató fiók (szolgáltatásnév) létrhozására és a Közreműködő szerepkör szolgáltatásnévhez való hozzárendelésére. 
- Javasolt művelet: Győződjön meg arról, hogy a bejelentkezett fiók hozzá van rendelve a "közreműködői", és próbálja megismételni a műveletet. Tekintse meg [ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) további információt a megfelelő engedélyek hozzárendelése a dokumentumot.
 
Után az automatikus frissítések is be van kapcsolva, a problémák a legtöbb is lehet kezelte a Site Recovery szolgáltatás, és megköveteli, hogy az a "**javítási**" gombra.

![javítás-gomb](./media/azure-to-azure-autoupdate/repair.png)

Amennyiben a javítás gomb nem érhető el, tekintse meg a bővítmény beállítások panel alatt jelenik meg hibaüzenet.

 - **Hiba**: A futtató fióknak nincs engedélye a recovery services-erőforrás eléréséhez.

    **Javasolt művelet**: Törölje, majd [hozza létre újból a futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account) vagy győződjön meg róla, hogy az Automation futtató fiókot az Azure Active Directory-alkalmazás hozzáfér a recovery services-erőforrás.

- **Hiba**: Futtató fiók nem található. Vagy ezek egyikét törölték, vagy nem lett létrehozva: Azure Active Directory-alkalmazás, szolgáltatásnév, szerepkör, Automation-tanúsítvány, Automation-kapcsolatobjektum - vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat. 

    **Javasolt művelet**: Törlése és [hozza létre újra a futtató fiókot](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
