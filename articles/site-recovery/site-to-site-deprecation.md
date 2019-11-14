---
title: Az ügyfelek által felügyelt helyek (VMM) közötti vész-helyreállítás elavult Azure Site Recovery használatával | Microsoft Docs
description: A DR a Hyper-V-t használó és a SCVMM által az Azure-ba és az alternatív lehetőségekkel felügyelt helyek közötti, az ügyfél által birtokolt webhelyek közötti közelgő elavult elavulás
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: rajanaki
ms.openlocfilehash: 1358b9056a79b8d9680e1e7b2fd207bdefd44b52
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040397"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Az ügyfelek által felügyelt helyek (VMM) közötti vész-helyreállítás elavult Azure Site Recovery használatával

Ez a cikk a közelgő elavult tervet, a kapcsolódó következményeket és az ügyfelek számára elérhető alternatív lehetőségeket ismerteti a következő esetekben:

DR System Center Virtual Machine Manager (SCVMM) által kezelt, az ügyfél által birtokolt helyek között Site Recovery

> [!IMPORTANT]
> Javasoljuk, hogy a legkorábbi módon végezze el a Szervizelési lépéseket, hogy elkerülje a környezet megszakadását. 

## <a name="what-changes-should-you-expect"></a>Milyen módosításokat kell elvárnia?

- November 2019-től kezdődően a rendszer nem engedélyezi az új felhasználók beszállását ezen forgatókönyvek esetén. A **meglévő replikálások és felügyeleti műveletek** , beleértve a feladatátvételt, a feladatátvételi tesztet, a figyelést stb. **nem érintik**.

- Ha rendelkezik meglévő konfigurációval, nem regisztrálhat új VMMs.

- Ha a forgatókönyvek elavultak, kivéve, ha az ügyfél nem követi az alternatív módszereket, a meglévő replikálások megszakadnak. Az ügyfelek nem tekinthetik meg, kezelhetik és nem hajthatják végre a DR-vel kapcsolatos műveleteket a Azure Portal Azure Site Recovery felületén keresztül.
 
## <a name="alternatives"></a>Alternatívák 

Az alábbiakban azokat az alternatívákat láthatja, amelyekkel az ügyfél kiválaszthatja, hogy a DR-stratégiájuk ne legyen hatással a forgatókönyv elavulása után. 

- 1\. lehetőség (ajánlott): válassza az [Azure-t a Hyper-V-gazdagépeken futó virtuális gépekhez tartozó Dr célként](hyper-v-azure-tutorial.md).

    > [!IMPORTANT]
    > Vegye figyelembe, hogy a helyszíni környezet továbbra is rendelkezhet SCVMMM, de az ASR-t csak a Hyper-V-gazdagépekre mutató hivatkozásokkal konfigurálhatja.

- 2\. lehetőség: válassza ki a helyek közötti replikálást a mögöttes [Hyper-V replika megoldás](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)használatával, de nem tudja kezelni a Dr-konfigurációkat a Azure Portal Azure site Recovery használatával. 


## <a name="remediation-steps"></a>Szervizelési lépések

Ha az 1. lehetőséget választja, hajtsa végre a következő lépéseket:

1. [Tiltsa le a VMMs társított összes virtuális gép védelmét](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). A replikáció **letiltása és eltávolítása** lehetőséggel vagy a megemlített szkriptek futtatásával gondoskodhat arról, hogy a helyszíni replikációs beállítások törlődnek. 

2. [Az összes VMM-kiszolgáló regisztrációjának törlése](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)

3. [Készítse elő az Azure-erőforrásokat](tutorial-prepare-azure-for-hyperv.md) a virtuális gépek replikálásának engedélyezéséhez.
4. [Helyszíni Hyper-V kiszolgálók előkészítése](hyper-v-prepare-on-premises-tutorial.md)

> [!IMPORTANT]
> Vegye figyelembe, hogy nem kell végrehajtania a VMM előkészítése szakaszban leírt lépéseket.

5. [A virtuális gépek replikálásának beállítása](hyper-v-azure-tutorial.md)
6. Nem kötelező, de ajánlott: [Dr-részletezés futtatása](tutorial-dr-drill-azure.md)

Ha a Hyper-V replika használatának 2. lehetőségét választja, hajtsa végre a következő lépéseket:

1. A **védett elemek** > **replikált elemek**területen kattintson a jobb gombbal a gépre, > **Tiltsa le a replikációt**.
2. A **replikáció letiltása**lapon válassza az **Eltávolítás**lehetőséget.

    Ezzel eltávolítja a replikált elemet a Azure Site Recoveryról (a számlázás leállt). A helyszíni virtuális gépen a replikálási konfiguráció **nem** lesz törölve. 

## <a name="next-steps"></a>Következő lépések
Tervezze meg az elavult alkalmazást, és válasszon egy alternatív lehetőséget, amely az infrastruktúra és a vállalat számára legmegfelelőbb. Ha bármilyen kérdése van, kérjük, lépjen kapcsolatba Microsoft ügyfélszolgálata

