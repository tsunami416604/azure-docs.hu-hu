---
title: Partner-azonosító csatolása a partner által a delegált erőforrásokon felhasználható kreditek engedélyezéséhez
description: Megtudhatja, hogyan rendelheti hozzá partner-AZONOSÍTÓját az Azure Lighthouse használatával kezelt felhasználói erőforrásokhoz kapcsolódó partneri kreditek (PEC) fogadásához.
ms.date: 09/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a9e7f51e90b38bad24eada5a665ca60bf43452f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493340"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Partner-azonosító csatolása a partner által a delegált erőforrásokon felhasználható kreditek engedélyezéséhez

Ha Ön a [Microsoft Partner Network](https://partner.microsoft.com/)tagja, összekapcsolhatja a Partner azonosítóját a delegált ügyfelek erőforrásainak kezeléséhez használt hitelesítő adatokkal. Így nyomon követheti az ügyfelek bevonásait és a [felügyelt szolgáltatásokra (PEC) vonatkozó partneri kreditek](/partner-center/partner-earned-credit)fogadását.

Ha az [Azure Marketplace-en felügyelt szolgáltatási ajánlatokat használó ügyfeleket](publish-managed-services-offers.md)készít, ez a hivatkozás automatikusan megtörténik, az ajánlatok közzétételéhez használt FIÓKPARTNER-azonosítóval. Ezen ügyfelek esetében nincs szükség további műveletre a PEC fogadásához.

Ha az [ügyfeleket Azure Resource Management-sablonok használatával](onboard-customer.md)készíti elő, a hivatkozás létrehozásához műveletet kell végrehajtania. Ezt úgy teheti meg, hogy az [MPN-azonosítót összekapcsolja](../../cost-management-billing/manage/link-partner-id.md) legalább egy olyan felhasználói fiókkal, amely az összes előfizetett előfizetéshez hozzáfér.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Partner-azonosító hozzárendelése új ügyfelek bevezetéséhez

Ha az ügyfeleket Azure Resource Manager-sablonokon keresztül készíti elő, a következő eljárással kapcsolhatja össze partneri AZONOSÍTÓját, és engedélyezheti a partner által létrehozott krediteket. A lépések elvégzéséhez ismernie kell az [MPN-Partner azonosítóját](/partner-center/partner-center-account-setup#locate-your-mpn-id) .

Az egyszerűség kedvéért javasoljuk, hogy hozzon létre egy egyszerű szolgáltatásnevet a bérlőben, társítsa az MPN-AZONOSÍTÓhoz, majd minden ügyfél számára hozzáférést biztosítson a [PEC-re jogosult Azure beépített szerepkörhöz](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Hozzon létre egy egyszerű szolgáltatásnevet](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) a kezelő bérlőben. Ebben a példában az egyszerű szolgáltatásnév-Automation-fiókot fogjuk elnevezni.
1. Az egyszerű szolgáltatás fiókjának használatával [csatolja a Partner azonosítóját](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) a felügyeleti bérlőben. Ezt csak egyszer kell elvégeznie.
1. Ha [Azure Resource Manager-sablonokkal](onboard-customer.md)végez bevezetést, ügyeljen arra, hogy olyan hitelesítést tartalmazzon, amely tartalmazza a PEC Automation-fiókot olyan [Azure beépített szerepkörrel](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)rendelkező felhasználóként, amely jogosult a PEC használatára.

A lépéseket követve minden Ön által kezelt ügyfél-bérlő társítva lesz a partner-AZONOSÍTÓhoz, amely lehetővé teszi, hogy a PEC-t fogadja az ügyfelek számára. A PEC Automation-fióknak nem kell hitelesítenie vagy végrehajtania semmilyen műveletet az ügyfél bérlője számára.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>A partner-azonosító hozzáadása a korábban bekészített ügyfelekhez

Ha már előkészített egy ügyfelet, előfordulhat, hogy nem kíván újabb központi telepítést végrehajtani a PEC Automation-fiók egyszerű telepítéséhez. Ehelyett társíthatja az MPN-azonosítót egy olyan felhasználói fiókkal, amelynek már van hozzáférése az ügyfél bérlője munkahelyéhez. Győződjön meg arról, hogy a fiók olyan [Azure beépített szerepkört kapott, amely jogosult a PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)használatára.

Miután a fiók [társítva lett a partner-azonosítóhoz](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) a felügyeleti bérlőben, a PEC-t az adott ügyfél számára is megkapja.

## <a name="confirm-partner-earned-credit"></a>Partner által létrehozott kredit megerősítése

[A PEC részleteit a Azure Portalban tekintheti](/partner-center/partner-earned-credit-explanation#azure-cost-management) meg, és megerősítheti, hogy a PEC Milyen költségekkel részesült.

Ha követte a fenti lépéseket, és nem látja a társítást, nyisson meg egy támogatási kérést a Azure Portal.

## <a name="next-steps"></a>Következő lépések

- További információ a [Microsoft Partner Network](/partner-center/mpn-overview)csatlakoztatásáról.
- Útmutató [a PEC kiszámításához és kifizetéséhez](/partner-center/partner-earned-credit-explanation).
- Az [ügyfelek](onboard-customer.md) bevonása az Azure Lighthouse-ba.
